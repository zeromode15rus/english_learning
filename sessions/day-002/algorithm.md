# Day 002 — Алгоритм: Мемоизация (Memoization)

---

## Интуиция

Представь, что тебя попросили вычислить 15-е число Фибоначчи. Наивный рекурсивный алгоритм вычислит fib(13) дважды, fib(12) — четырежды, fib(2) — сотни раз. Это катастрофа: экспоненциальное дублирование работы.

Мемоизация — простая идея: вычисли один раз, запомни результат, при повторном вызове верни из кэша. Это «top-down» динамическое программирование: рекурсия сохраняет работу вместо того, чтобы повторять её.

---

## Трассировка: fib(5)

**Без мемоизации** — дерево вызовов:

```
fib(5)
├── fib(4)
│   ├── fib(3)
│   │   ├── fib(2) → 1
│   │   └── fib(1) → 1
│   └── fib(2) → 1   ← повтор
└── fib(3)           ← повтор
    ├── fib(2) → 1   ← повтор
    └── fib(1) → 1
```

Итого вызовов: 15. Для n=50 — больше триллиона.

**С мемоизацией** — каждое значение вычисляется ровно один раз:

```
Вызов fib(5):
  fib(4) → не в кэше → вычисляем
    fib(3) → не в кэше → вычисляем
      fib(2) → не в кэше → вычисляем → cache[2] = 1 ✓
      fib(1) → базовый → 1
    cache[3] = 2 ✓
    fib(2) → cache hit! → 1 (без вычисления)
  cache[4] = 3 ✓
  fib(3) → cache hit! → 2 (без вычисления)
cache[5] = 5 ✓

Итого вычислений: 5 (по одному для каждого значения от 1 до 5)
```

---

## Реализация: универсальный декоратор мемоизации

```js
function memoize(fn) {
  const cache = new Map();

  return function (...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);

    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}
```

**Важно:** `fib` ссылается на саму себя по имени — это обязательно для мемоизированной рекурсии:

```js
const fib = memoize(function (n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2); // fib — это уже мемоизированная версия
});

fib(50); // 12586269025 — мгновенно
```

Если передать анонимную функцию и вызывать её имя внутри — рекурсия пойдёт в немемоизированную версию и ничего не кэшируется.

---

## Три реальных задачи с трассировкой

### Задача 1: Coin Change — количество вариантов набрать сумму

**Задача:** сколькими способами можно набрать сумму `amount` из монет `coins`?

```js
const countWays = memoize(function (amount, coins) {
  if (amount === 0) return 1;       // нашли один способ
  if (amount < 0) return 0;         // зашли за ноль — тупик

  return coins.reduce((total, coin) => {
    return total + countWays(amount - coin, coins);
  }, 0);
});
```

**Трассировка** для `amount=4`, `coins=[1,2]`:

```
countWays(4) = countWays(3) + countWays(2)
  countWays(3) = countWays(2) + countWays(1)
    countWays(2) = countWays(1) + countWays(0)
      countWays(1) = countWays(0) + countWays(-1)
        countWays(0) = 1
        countWays(-1) = 0
      = 1
      countWays(0) = 1  ← cache hit
    countWays(2) = 2  → кэшируем
    countWays(1) = 1  ← cache hit
  countWays(3) = 3  → кэшируем
  countWays(2) = 2  ← cache hit
countWays(4) = 5
```

Пять способов: [1,1,1,1], [1,1,2], [1,2,1], [2,1,1], [2,2].

### Задача 2: Longest Common Subsequence (LCS)

**Задача:** найти длину наибольшей общей подпоследовательности двух строк.

```js
const lcs = memoize(function (a, b, i = 0, j = 0) {
  if (i === a.length || j === b.length) return 0;

  if (a[i] === b[j]) {
    return 1 + lcs(a, b, i + 1, j + 1);   // символы совпали — берём оба
  }

  return Math.max(
    lcs(a, b, i + 1, j),   // пропускаем символ из a
    lcs(a, b, i, j + 1)    // пропускаем символ из b
  );
});
```

**Трассировка** для `"ace"` и `"abcde"`:

```
lcs("ace","abcde", 0, 0): a==a → 1 + lcs(1,1)
  lcs(1,1): c vs b → max(lcs(2,1), lcs(1,2))
    lcs(2,1): e vs b → max(lcs(3,1)=0, lcs(2,2))
      lcs(2,2): e vs c → max(lcs(3,2)=0, lcs(2,3))
        lcs(2,3): e vs d → max(lcs(3,3)=0, lcs(2,4))
          lcs(2,4): e==e → 1 + lcs(3,5)=0 → 1
        → 1
      → 1
    → 1
    lcs(1,2): c==c → 1 + lcs(2,3)  ← cache hit
      → 1 + 1 = 2
  → max(1, 2) = 2
→ 1 + 2 = 3

LCS = 3, последовательность: "ace"
```

### Задача 3: Grid Paths — количество путей в сетке

**Задача:** сколько путей из (0,0) в (m,n) двигаясь только вправо или вниз?

```js
const gridPaths = memoize(function (m, n) {
  if (m === 0 || n === 0) return 1;  // один путь вдоль края
  return gridPaths(m - 1, n) + gridPaths(m, n - 1);
});
```

**Трассировка** для 2×2 (путь из (2,2) в (0,0)):

```
gridPaths(2,2) = gridPaths(1,2) + gridPaths(2,1)
  gridPaths(1,2) = gridPaths(0,2) + gridPaths(1,1)
    gridPaths(0,2) = 1
    gridPaths(1,1) = gridPaths(0,1) + gridPaths(1,0) = 1 + 1 = 2
  = 1 + 2 = 3
  gridPaths(2,1) = gridPaths(1,1) + gridPaths(2,0)
    gridPaths(1,1) ← cache hit = 2
    gridPaths(2,0) = 1
  = 2 + 1 = 3
= 3 + 3 = 6
```

---

## Big O

| | Время | Память |
|---|---|---|
| Fibonacci без мемоизации | O(2ⁿ) | O(n) — стек |
| Fibonacci с мемоизацией | O(n) | O(n) — кэш + стек |
| LCS без мемоизации | O(2^(m+n)) | O(m+n) |
| LCS с мемоизацией | O(m·n) | O(m·n) |
| Grid Paths с мемоизацией | O(m·n) | O(m·n) |

---

## Best Practices

### 1. Ключ кэша — главная проблема

`JSON.stringify(args)` работает, но:
- Медленно для больших объектов
- Не различает `undefined` и `null` в некоторых случаях
- Не работает для функций и циклических ссылок

Лучше для примитивных аргументов:
```js
const key = args.join(','); // быстрее для чисел и строк
```

Для объектов — WeakMap (не удерживает объект в памяти):
```js
function memoizeObject(fn) {
  const cache = new WeakMap();
  return function (obj) {
    if (cache.has(obj)) return cache.get(obj);
    const result = fn(obj);
    cache.set(obj, result);
    return result;
  };
}
```

### 2. Ограничение размера кэша — LRU

Безграничный кэш — утечка памяти. В production нужен LRU (Least Recently Used):

```js
class LRUCache {
  constructor(capacity) {
    this.capacity = capacity;
    this.map = new Map(); // Map сохраняет порядок вставки
  }

  get(key) {
    if (!this.map.has(key)) return -1;
    const val = this.map.get(key);
    this.map.delete(key);    // удаляем
    this.map.set(key, val);  // вставляем в конец (= "недавно использован")
    return val;
  }

  put(key, value) {
    if (this.map.has(key)) this.map.delete(key);
    if (this.map.size >= this.capacity) {
      // удаляем самый старый (первый в Map)
      this.map.delete(this.map.keys().next().value);
    }
    this.map.set(key, value);
  }
}
```

### 3. Мемоизация vs табуляция

Мемоизация (top-down) — рекурсия с кэшем. Удобна, когда не нужны все подзадачи.

Табуляция (bottom-up) — итеративное заполнение таблицы снизу вверх. Нет накладных расходов на стек, часто быстрее на практике:

```js
// Fibonacci: табуляция
function fibTab(n) {
  if (n <= 1) return n;
  const dp = [0, 1];
  for (let i = 2; i <= n; i++) {
    dp[i] = dp[i-1] + dp[i-2];
  }
  return dp[n];
}

// Оптимизация памяти — O(1) вместо O(n):
function fibOpt(n) {
  let [a, b] = [0, 1];
  for (let i = 2; i <= n; i++) [a, b] = [b, a + b];
  return b;
}
```

### 4. Мемоизация в React

**React.memo** — мемоизация компонента: не ре-рендерится если props не изменились (shallow comparison).
```js
const Button = React.memo(({ label, onClick }) => (
  <button onClick={onClick}>{label}</button>
));
// Внимание: если onClick — новая функция при каждом рендере родителя,
// React.memo не поможет! Нужен useCallback.
```

**useMemo** — мемоизация вычисленного значения:
```js
// БЕЗ useMemo — sortedList пересчитывается при каждом рендере
const sortedList = [...items].sort(compareFn);

// С useMemo — пересчёт только при изменении items
const sortedList = useMemo(() => [...items].sort(compareFn), [items]);
```

**useCallback** — мемоизация функции (стабилизация ссылки):
```js
// Каждый рендер — новая функция → React.memo в дочернем бесполезен
const handleClick = () => dispatch(action);

// useCallback — та же ссылка пока dispatch не изменился
const handleClick = useCallback(() => dispatch(action), [dispatch]);
```

**Типичная ошибка:** `useMemo` для дешёвых вычислений. Мемоизация сама по себе стоит памяти и сравнения зависимостей. Не используй useMemo для `items.length` или `a + b`.

---

## Когда применять — когда нет

**Применять:**
- Рекурсия с перекрывающимися подзадачами (Fibonacci, Coin Change, LCS, Grid Paths)
- Дорогостоящие вычисления в React-компонентах (сортировка/фильтрация больших списков)
- Redux-селекторы (Reselect) — производное состояние из всего стора
- Функции с детерминированным выходом (тот же вход → тот же выход всегда)

**Не применять:**
- Функция каждый раз вызывается с уникальными аргументами — кэш не даёт попаданий
- Вычисление дешёвое — накладные расходы на Map и JSON.stringify превышают выигрыш
- Функция имеет побочные эффекты (API calls, запись в БД) — кэш вернёт старый результат
- Бесконечный ввод — нужен LRU, иначе утечка памяти

---

## Вопросы для интервью

**1.** Напиши мемоизированную функцию для задачи coin change: количество минимальных монет для суммы `amount`. Покажи трассировку для `coins=[1,5,6]`, `amount=9`. Почему ответ не 5+1+1+1+1, а 6+3?

**2.** В чём разница между `useMemo` и `useCallback` в React? Когда `useCallback` не поможет избежать лишних ре-рендеров дочернего компонента, обёрнутого в `React.memo`?

**3.** Почему для мемоизации с объектными аргументами лучше использовать `WeakMap` вместо `Map`? Что произойдёт с памятью если использовать `Map` с объектами-ключами в React-компоненте?
