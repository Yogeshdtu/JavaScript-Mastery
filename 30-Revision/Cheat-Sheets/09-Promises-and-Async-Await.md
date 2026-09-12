# Cheat Sheet: Promises and Async/Await

**Full detail:** [13-Asynchronous-JavaScript/](../../13-Asynchronous-JavaScript/), [14-Promises-and-Async-Await/](../../14-Promises-and-Async-Await/)

---

## Promise States
`pending` → `fulfilled` OR `rejected` (permanent once settled)

```js
new Promise((resolve, reject) => { ... })
  .then(value => {})
  .catch(error => {})
  .finally(() => {});
```

## async/await
```js
async function fn() {
  try {
    const result = await somePromise; // Pauses THIS function only
    return result; // Always wrapped in a Promise
  } catch (error) {
    // Handles rejected 'await'
  }
}
```

## Combinators

| Method | Resolves When | Rejects When |
|--------|-----------------|-------------------|
| `Promise.all()` | ALL fulfill | ANY rejects (fail-fast) |
| `Promise.allSettled()` | ALL settle | Never |
| `Promise.race()` | FIRST settles | If first is a rejection |
| `Promise.any()` | FIRST fulfills | ALL reject |

## Sequential vs Parallel
```js
// ⚠️ Slow (sequential) — for INDEPENDENT operations
const a = await taskA(); const b = await taskB();

// ✅ Fast (parallel)
const [a, b] = await Promise.all([taskA(), taskB()]);
```

## Critical Gotcha
```js
fetch(url).then(res => res.json()); // fetch() does NOT reject on 404/500!
// Must manually check:
if (!response.ok) throw new Error(`HTTP ${response.status}`);
```

## Callback Hell → Fixed By
Promises (flat chaining) → async/await (synchronous-looking code)

---

**Next:** [10-Event-Loop.md](./10-Event-Loop.md)
