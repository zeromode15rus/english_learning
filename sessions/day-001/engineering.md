# Day 001 — Engineering Interview Theory

---

## Track A — System Design

### Вопрос: Как спроектировать URL-shortener (типа bit.ly)?

**Функциональные требования:**
- Принимает длинный URL, возвращает короткий (7 символов)
- По короткому URL делает redirect на длинный
- Желательно: аналитика (счётчик переходов), TTL, custom alias

**Оценка нагрузки:**
- 100M новых ссылок в день = ~1150 write RPS
- Read/write ratio = 100:1 → ~115 000 read RPS
- Хранение: 7 символов = base62, 62^7 ≈ 3.5 триллиона уникальных ссылок — запаса хватит

**Генерация короткого ключа — три подхода и их trade-offs:**

1. **MD5/SHA256 от длинного URL** → берём первые 7 символов. Просто, но коллизии возможны, и разные URL с одним контентом дадут один ключ — это может быть нежелательно.

2. **Глобальный счётчик + base62** → атомарный инкремент в Redis (`INCR counter`), результат переводим в base62. Гарантирует уникальность, предсказуемый порядок. Проблема: единая точка отказа (Redis), при горизонтальном масштабировании нужна координация.

3. **Distributed ID (Snowflake-like)** → каждый сервис генерирует ID самостоятельно из timestamp + machine ID + sequence. Нет координации, высокая пропускная способность. Используется в Twitter, Discord.

**Хранилище:**
- Каждая запись: `short_key (PK) → long_url, created_at, ttl, user_id`
- Объём: 500 байт/запись × 100M/день × 365 × 5 лет ≈ ~91 TB → нужен NoSQL (Cassandra, DynamoDB) или sharded PostgreSQL
- Чтение гораздо чаще записи → кеш обязателен

**Кеширование:**
- Redis с LRU eviction: 80/20 — 20% ключей дают 80% трафика
- TTL кеша: 24 часа, после — запрос в БД
- При cache miss → чтение из БД → запись в кеш

**Redirect:**
- HTTP 301 (Permanent) — браузер кешируют, нагрузка снижается, но аналитика теряется
- HTTP 302 (Temporary) — каждый раз через сервер, аналитика точная
- Выбор зависит от требований: если важна аналитика → 302

**Масштабирование:**
- Stateless API-серверы за load balancer → горизонтальное масштабирование тривиально
- Read replicas для БД
- CDN для статики, но redirect нужно делать через сервис (CDN не знает маппинг)

**Узкие места:**
- Генерация ключей при высоком RPS → pre-generate batch ключей и раздавать сервисам
- Hot keys (вирусные ссылки) → локальный кеш в каждом инстансе сервиса поверх Redis

---

## Track B — Frontend internals

### Вопрос: Как работает алгоритм reconciliation в React (diffing virtual DOM)?

Reconciliation — процесс, которым React решает, какие части реального DOM нужно обновить после изменения состояния. Наивное сравнение двух деревьев — O(n³). React использует эвристики, снижающие это до O(n).

**Две ключевые эвристики:**

**1. Элементы разного типа → полное пересоздание поддерева**

```jsx
// Было:
<div><Counter /></div>

// Стало:
<span><Counter /></span>
```

`div` → `span` — React не пытается переиспользовать: unmount старого дерева, mount нового. Это важно: если обернуть компонент в другой тег — он полностью перемонтируется и потеряет state.

**2. Элементы одного типа → обновление атрибутов, рекурсия вглубь**

```jsx
// Было:
<div className="old" style={{color: 'red'}} />

// Стало:
<div className="new" style={{color: 'blue'}} />
```

React обновляет только изменившиеся атрибуты — `className` и `color`. DOM-узел не пересоздаётся.

**3. Ключи (keys) в списках**

Без ключей React сравнивает элементы списка по позиции. При вставке в начало списка все элементы кажутся «изменившимися» → O(n) обновлений.

```jsx
// Плохо — без ключей:
<ul>
  {items.map(item => <li>{item.name}</li>)}
</ul>

// Хорошо:
<ul>
  {items.map(item => <li key={item.id}>{item.name}</li>)}
</ul>
```

С ключами React сопоставляет элементы по ключу, а не по позиции → при вставке в начало только один новый элемент создаётся, остальные переиспользуются.

**Почему нельзя использовать index как ключ:**
При вставке в начало списка индексы сдвигаются → React думает, что все элементы изменились. Плюс баги с input state: `<li key={0}>` после вставки указывает на другой элемент, но React считает его «тем же».

**React Fiber и reconciliation:**
В React 16+ reconciliation разделён на две фазы:
- **Render phase** (reconciler, может прерываться): строит дерево fiber-узлов, вычисляет diff — чисто вычислительная работа, без побочных эффектов
- **Commit phase** (renderer, синхронная): применяет изменения к реальному DOM, вызывает `useEffect`, `useLayoutEffect`

Разделение позволяет React прерывать render phase при поступлении более приоритетного обновления (concurrent mode).

```js
// Упрощённая логика reconcileChildren:
function reconcileChildren(currentFiber, newChildren) {
  let oldFiber = currentFiber.child;
  let newIndex = 0;

  while (oldFiber !== null || newIndex < newChildren.length) {
    const newChild = newChildren[newIndex];

    if (oldFiber && oldFiber.key === newChild.key && oldFiber.type === newChild.type) {
      // переиспользуем существующий fiber, обновляем props
      updateFiber(oldFiber, newChild.props);
    } else {
      // создаём новый fiber
      createFiber(newChild);
      if (oldFiber) deleteFiber(oldFiber); // помечаем старый на удаление
    }

    oldFiber = oldFiber?.sibling;
    newIndex++;
  }
}
```

---

## Track C — Backend and databases

### Вопрос: Что такое индексы в PostgreSQL, как они работают и когда их не нужно создавать?

**Что такое индекс**

Индекс — отдельная структура данных, которую PostgreSQL поддерживает параллельно с таблицей. При запросе с условием `WHERE` query planner решает: читать всю таблицу (sequential scan) или использовать индекс (index scan). Без индекса поиск по n строкам = O(n). С B-tree индексом = O(log n).

**Как устроен B-tree индекс (по умолчанию)**

B-tree (Balanced Tree) — сбалансированное дерево поиска. Каждый узел хранит упорядоченные ключи и указатели на дочерние узлы или строки таблицы (heap tuples). Высота дерева растёт как log(n) — для таблицы с 1 миллиардом строк высота дерева ≈ 6–7 уровней.

```sql
CREATE INDEX idx_users_email ON users(email);

EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'user@example.com';
-- Index Scan using idx_users_email on users
-- (cost=0.43..8.45 rows=1 width=120) (actual time=0.032..0.033 rows=1 loops=1)
```

PostgreSQL также поддерживает:
- **Hash index** — только для `=`, O(1) в среднем, не поддерживает сортировку и range queries
- **GIN (Generalized Inverted Index)** — для полнотекстового поиска, массивов, JSONB
- **GiST** — для геометрических данных, IP-диапазонов
- **BRIN** — для очень больших таблиц с физически упорядоченными данными (timestamp логи)

**Составные индексы и порядок колонок**

```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

Этот индекс работает для:
- `WHERE user_id = 5` ✅
- `WHERE user_id = 5 AND status = 'pending'` ✅
- `WHERE status = 'pending'` ❌ — первая колонка должна присутствовать

Правило: наиболее селективная колонка (с большим количеством уникальных значений) — первой, если нет специфических требований.

**Когда индекс НЕ нужен**

- Таблица маленькая (< 1000 строк) — sequential scan быстрее из-за отсутствия overhead
- Колонка с низкой кардинальностью: `status` с 3 значениями — индекс почти бесполезен, planner выберет seq scan
- Колонка часто обновляется — каждый `UPDATE` перестраивает индекс, это дорого
- Таблица с высокой частотой INSERT/DELETE — индекс замедляет запись

**Partial index — когда нужен индекс только по части строк**

```sql
-- Индексируем только незакрытые задачи (их мало, но по ним чаще ищут)
CREATE INDEX idx_tasks_open ON tasks(created_at)
WHERE status != 'closed';
```

**Bloat и REINDEX**

После большого количества UPDATE/DELETE индекс может «раздуться» (bloat) — содержать мёртвые страницы. `VACUUM` убирает мёртвые строки, `REINDEX CONCURRENTLY` перестраивает индекс без блокировки таблицы.

```sql
-- Проверить размер и bloat индексов:
SELECT indexname, pg_size_pretty(pg_relation_size(indexname::regclass))
FROM pg_indexes
WHERE tablename = 'users';
```
