# Quick Revision: Modern JS to Async (Modules 10-16)

**Previous:** [02-Arrays-Objects-Scope-Coercion.md](./02-Arrays-Objects-Scope-Coercion.md)
**Next:** [04-Backend-to-Advanced.md](./04-Backend-to-Advanced.md)

---

## Module 10 — Modern JavaScript
- Classes = syntactic sugar over prototypes; `super()` before `this` in child constructors.
- `Map` (any key type) / `Set` (unique values) vs `WeakMap`/`WeakSet` (object-only, garbage-collectible).
- Generators (`function*`, `yield`) pause/resume execution — power `for...of` via `Symbol.iterator`.
- Symbols = always-unique keys, hidden from normal enumeration.
- Private fields (`#field`) = true, engine-enforced privacy.

## Module 11-12 — DOM, Browser, Events
- `textContent` (safe) vs `innerHTML` (XSS-risk with untrusted input).
- `classList.add/remove/toggle` over `className =` (which overwrites).
- Events bubble (default) then reach capturing listeners first if registered with `true`.
- Event delegation: one listener on parent handles all (including future) children.
- `localStorage` (permanent) / `sessionStorage` (tab) / cookies (sent to server).

## Module 13-14 — Async JavaScript
- Single-threaded — async ops handed to Web APIs/libuv, not the Call Stack.
- Promises: pending → fulfilled/rejected (permanent). `.then/.catch/.finally`.
- `async` functions always return a Promise; `await` pauses only that function.
- `Promise.all` (fail-fast) / `allSettled` (never fails) / `race` (fastest settles) / `any` (fastest success).
- Independent async ops → run in parallel (`Promise.all`), not sequential `await`.

## Module 15 — Event Loop
- Call Stack → (empty) → drain ENTIRE Microtask Queue → ONE Macrotask → repeat.
- Microtasks (Promises) always beat Macrotasks (`setTimeout`), even at `0ms`.

## Module 16 — Fetch and APIs
- `fetch()` only rejects on network failure — ALWAYS check `response.ok` for HTTP errors.
- CRUD → HTTP: Create=POST, Read=GET, Update=PUT/PATCH, Delete=DELETE.
- CORS is a browser + server-header mechanism — fixed server-side, never in frontend code.

## Self-Check
- [ ] Can explain why classes aren't "real" classes
- [ ] Can trace a mixed setTimeout/Promise snippet's exact output order
- [ ] Know the `fetch()` "resolves on 404" gotcha
- [ ] Can explain event delegation with a code example

---

**Next:** [04-Backend-to-Advanced.md](./04-Backend-to-Advanced.md)
