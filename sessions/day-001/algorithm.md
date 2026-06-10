# Day 001 — Алгоритмы: Поиск (Search)

> Теория: `theory/algorithms/01-search.md`
> Здесь — решённые задачи с полным разбором и best practices.

---

## Задача 1: Binary Search — базовая (LeetCode 704)

**Условие:** дан отсортированный массив `nums` и число `target`. Найди индекс `target`. Если не найден — верни -1. Сложность должна быть O(log n).

**Разбор:** массив отсортирован → бинарный поиск. Делим пополам, сравниваем с серединой, отбрасываем половину.

**Трассировка:** `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9`

```
left=0, right=5

Шаг 1: mid = (0+5)//2 = 2 → nums[2] = 3. 3 < 9 → ищем правее → left = 3
Шаг 2: mid = (3+5)//2 = 4 → nums[4] = 9. НАШЛИ → return 4
```

```js
function search(nums, target) {
  let left = 0;
  let right = nums.length - 1;

  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2); // защита от overflow
    if (nums[mid] === target) return mid;
    if (nums[mid] < target) left = mid + 1;
    else right = mid - 1;
  }

  return -1;
}
```

**Почему `left + (right - left) / 2` а не `(left + right) / 2`?**
В языках с фиксированными 32-битными int (Java, C++) `left + right` может переполниться при больших индексах. В JS числа 64-битные float — переполнения нет, но паттерн стоит знать для интервью.

**Big O:** O(log n) время, O(1) память.

---

## Задача 2: Search in Rotated Sorted Array (LeetCode 33)

**Условие:** массив был отсортирован, а потом повёрнут в неизвестной точке: `[4,5,6,7,0,1,2]`. Найди `target`. O(log n).

**Разбор:** обычный бинарный поиск не работает — массив не строго отсортирован. Но: при любом делении пополам **хотя бы одна половина всегда отсортирована**. Определяем какая — и решаем, в какой половине искать.

**Трассировка:** `nums = [4,5,6,7,0,1,2]`, `target = 0`

```
left=0, right=6

Шаг 1: mid=3, nums[3]=7
  Левая половина [4,5,6,7] — отсортирована? nums[left]=4 ≤ nums[mid]=7 → да
  target=0 в диапазоне [4..7]? Нет → ищем в правой → left=4

Шаг 2: left=4, right=6, mid=5, nums[5]=1
  Левая половина [0,1] — отсортирована? nums[left]=0 ≤ nums[mid]=1 → да
  target=0 в диапазоне [0..1]? Да → ищем в левой → right=4

Шаг 3: left=4, right=4, mid=4, nums[4]=0 === target → return 4
```

```js
function searchRotated(nums, target) {
  let left = 0;
  let right = nums.length - 1;

  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (nums[mid] === target) return mid;

    // левая половина отсортирована
    if (nums[left] <= nums[mid]) {
      if (nums[left] <= target && target < nums[mid]) {
        right = mid - 1; // target в левой половине
      } else {
        left = mid + 1;  // target в правой половине
      }
    } else {
      // правая половина отсортирована
      if (nums[mid] < target && target <= nums[right]) {
        left = mid + 1;  // target в правой половине
      } else {
        right = mid - 1; // target в левой половине
      }
    }
  }

  return -1;
}
```

**Big O:** O(log n) время, O(1) память.

**Частая ошибка:** проверка `nums[left] <= nums[mid]` (с `<=`) а не `<`. Если `left === mid`, они равны — это не ошибка, просто массив из одного элемента.

---

## Задача 3: First and Last Position (LeetCode 34)

**Условие:** дан отсортированный массив с повторами, найди первый и последний индекс `target`. Если не найден — `[-1, -1]`.

**Разбор:** два бинарных поиска — один ищет самое левое вхождение, другой — самое правое.

**Трассировка:** `nums = [5,7,7,8,8,10]`, `target = 8`

```
Поиск левой границы:
left=0, right=5
  mid=2, nums[2]=7 < 8 → left=3
  mid=4, nums[4]=8 = target → result=4, right=3  (запоминаем, но продолжаем влево)
  mid=3, nums[3]=8 = target → result=3, right=2
  left > right → стоп. Левая граница = 3

Поиск правой границы:
left=0, right=5
  mid=2, nums[2]=7 < 8 → left=3
  mid=4, nums[4]=8 = target → result=4, left=5  (продолжаем вправо)
  mid=5, nums[5]=10 > 8 → right=4
  left > right → стоп. Правая граница = 4

Ответ: [3, 4]
```

```js
function searchRange(nums, target) {
  return [findBound(nums, target, true), findBound(nums, target, false)];
}

function findBound(nums, target, isLeft) {
  let left = 0;
  let right = nums.length - 1;
  let result = -1;

  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);

    if (nums[mid] === target) {
      result = mid;
      if (isLeft) right = mid - 1; // продолжаем искать левее
      else        left  = mid + 1; // продолжаем искать правее
    } else if (nums[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}
```

**Big O:** O(log n) время, O(1) память.

---

## Задача 4: BFS — кратчайший путь в лабиринте

**Условие:** дана сетка `grid` (0 = проход, 1 = стена). Найди длину кратчайшего пути из левого верхнего угла в правый нижний. Движение — 4 направления. Если пути нет — вернуть -1.

**Разбор:** BFS гарантирует кратчайший путь в невзвешенном графе. Каждая клетка — вершина, соседи — рёбра. Идём волнами: сначала все клетки на расстоянии 1, потом 2 и т.д.

**Трассировка:** `grid = [[0,0,0],[1,1,0],[0,0,0]]`

```
Старт: (0,0), финиш: (2,2)
Очередь: [(0,0, steps=1)]
Посещённые: {(0,0)}

Шаг 1: берём (0,0) steps=1
  Соседи: (0,1) → добавляем
  (1,0) → стена, пропускаем
  Очередь: [(0,1, steps=2)]

Шаг 2: берём (0,1) steps=2
  Соседи: (0,0) → посещён; (0,2) → добавляем; (1,1) → стена
  Очередь: [(0,2, steps=3)]

Шаг 3: берём (0,2) steps=3
  Соседи: (1,2) → добавляем
  Очередь: [(1,2, steps=4)]

Шаг 4: берём (1,2) steps=4
  Соседи: (2,2) → добавляем
  Очередь: [(2,2, steps=5)]

Шаг 5: берём (2,2) — это финиш → return 5
```

```js
function shortestPath(grid) {
  const rows = grid.length;
  const cols = grid[0].length;

  if (grid[0][0] === 1 || grid[rows-1][cols-1] === 1) return -1;

  const dirs = [[0,1],[0,-1],[1,0],[-1,0]];
  const queue = [[0, 0, 1]]; // [row, col, steps]
  const visited = new Set(['0,0']);

  while (queue.length > 0) {
    const [r, c, steps] = queue.shift();

    if (r === rows-1 && c === cols-1) return steps;

    for (const [dr, dc] of dirs) {
      const nr = r + dr;
      const nc = c + dc;
      const key = `${nr},${nc}`;

      if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
          && grid[nr][nc] === 0 && !visited.has(key)) {
        visited.add(key);
        queue.push([nr, nc, steps + 1]);
      }
    }
  }

  return -1;
}
```

**Big O:** O(rows × cols) время и память — в худшем случае обходим все клетки.

---

## Задача 5: DFS — обнаружение цикла в ориентированном графе

**Условие:** дан ориентированный граф. Определи, есть ли в нём цикл.

**Разбор:** DFS с тремя состояниями вершины:
- `WHITE (0)` — не посещена
- `GRAY (1)` — в текущем пути (в стеке рекурсии)
- `BLACK (2)` — полностью обработана

Цикл есть, если при обходе DFS мы попадаем в `GRAY`-вершину — это значит мы вернулись в вершину, которая ещё не закрыта.

**Трассировка:** граф `{0→1, 1→2, 2→0}` (цикл)

```
dfs(0): 0 → GRAY
  dfs(1): 1 → GRAY
    dfs(2): 2 → GRAY
      сосед 0: состояние = GRAY → ЦИКЛ НАЙДЕН → return true
```

**Граф без цикла** `{0→1, 0→2, 1→3}`:

```
dfs(0): 0 → GRAY
  dfs(1): 1 → GRAY
    dfs(3): 3 → GRAY → 3 → BLACK (нет соседей)
  1 → BLACK
  dfs(2): 2 → GRAY → 2 → BLACK
0 → BLACK
Цикла нет.
```

```js
function hasCycle(numNodes, edges) {
  // строим список смежности
  const graph = Array.from({ length: numNodes }, () => []);
  for (const [from, to] of edges) graph[from].push(to);

  const WHITE = 0, GRAY = 1, BLACK = 2;
  const color = new Array(numNodes).fill(WHITE);

  function dfs(node) {
    color[node] = GRAY;

    for (const neighbor of graph[node]) {
      if (color[neighbor] === GRAY) return true;  // нашли цикл
      if (color[neighbor] === WHITE && dfs(neighbor)) return true;
    }

    color[node] = BLACK;
    return false;
  }

  for (let i = 0; i < numNodes; i++) {
    if (color[i] === WHITE && dfs(i)) return true;
  }

  return false;
}

// Пример:
hasCycle(3, [[0,1],[1,2],[2,0]]); // true — цикл 0→1→2→0
hasCycle(4, [[0,1],[0,2],[1,3]]); // false
```

**Big O:** O(V + E) время, O(V) память (стек рекурсии + массив цвета).

---

## Best Practices

### Бинарный поиск

**1. Инвариант цикла:** перед каждой итерацией ответ всегда находится в `[left, right]`. Нарушение инварианта — источник off-by-one ошибок.

**2. Условие выхода:** `while (left <= right)` — для поиска точного значения. `while (left < right)` — для поиска границы (left и right сходятся к одной точке).

**3. Шаблон для поиска левой границы:**
```js
// Найти первый индекс, где nums[i] >= target
function lowerBound(nums, target) {
  let left = 0, right = nums.length;
  while (left < right) {
    const mid = left + Math.floor((right - left) / 2);
    if (nums[mid] < target) left = mid + 1;
    else right = mid;
  }
  return left; // left === right — первый индекс >= target
}
```

### BFS

**1. Всегда помечай посещённые ДО добавления в очередь**, а не после извлечения. Иначе одна вершина попадёт в очередь несколько раз — лишняя работа или бесконечный цикл.

```js
// Плохо:
queue.push(neighbor);
// ...
const node = queue.shift();
visited.add(node); // поздно — node мог попасть в очередь дважды

// Хорошо:
visited.add(neighbor);
queue.push(neighbor);
```

**2. `queue.shift()` — O(n).** Для большого графа используй `deque` или указатель:
```js
let head = 0;
while (head < queue.length) {
  const node = queue[head++]; // O(1) вместо O(n)
}
```

### DFS

**1. Итеративный DFS ≠ рекурсивный DFS по порядку.** Итеративный использует стек явно — порядок обхода может отличаться. Для обнаружения циклов используй рекурсивный или эмулируй стек фреймов.

**2. Глубина рекурсии.** JavaScript по умолчанию имеет лимит стека ~10 000 вызовов. Для графов с миллионами вершин — итеративная DFS обязательна.

---

## Вопросы для интервью

**1.** Массив `[1,3,5,7,9,11,13]`. Бинарный поиск числа `7`. Покажи трассировку — значения `left`, `right`, `mid` на каждом шаге. Сколько шагов?

**2.** Почему в задаче LeetCode 33 (повёрнутый массив) при определении «какая половина отсортирована» используется `<=` (а не `<`) в условии `nums[left] <= nums[mid]`?

**3.** В BFS-лабиринте: что будет, если помечать клетки посещёнными ПОСЛЕ извлечения из очереди, а не до добавления? Дай конкретный пример сетки, где это приведёт к проблеме.
