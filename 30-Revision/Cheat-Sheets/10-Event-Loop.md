# Cheat Sheet: Event Loop

**Full detail:** [15-Event-Loop/](../../15-Event-Loop/)

---

## The Components
- **Call Stack** — tracks current execution (single-threaded)
- **Web APIs / libuv** — handle async ops (timers, network, I/O) outside the engine
- **Macrotask Queue** — `setTimeout`, DOM events, I/O
- **Microtask Queue** — Promises, `async`/`await` continuations

## The Golden Rule
```
1. Run synchronous code (Call Stack)
2. Drain ENTIRE Microtask Queue (fully — including new ones added during draining)
3. Run ONE Macrotask
4. Go to step 2
```

## Priority
**Microtasks ALWAYS run before the next macrotask** — regardless of `setTimeout` delay value.

## Classic Example
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
// Output: A, D, C, B
```

## Danger: Microtask Starvation
If microtasks keep scheduling MORE microtasks, macrotasks (and rendering) can be indefinitely delayed.

## Quick Trace Method
1. List all sync `console.log`s in order.
2. List all microtasks (Promises) in the order they were scheduled.
3. List all macrotasks (setTimeout) in the order they were scheduled.
4. Output = Sync → Microtasks (fully drained) → repeat per Macrotask.

---

**Next:** [11-DOM-and-Events.md](./11-DOM-and-Events.md)
