# Quick Revision: Backend to Advanced (Modules 17-26)

**Previous:** [03-Modern-JS-to-Async.md](./03-Modern-JS-to-Async.md)
**Next:** [30-Revision/JavaScript-Gotchas.md](../JavaScript-Gotchas.md)

---

## Module 17 — Modules
- ES Modules (✅): `import`/`export`, async-capable, static-analyzable, browser-native.
- CommonJS (⚠️): `require`/`module.exports`, synchronous, Node's original system.

## Module 18-19 — Node.js and Backend
- Node = V8 (execution) + libuv (non-blocking I/O).
- `dependencies` (production) vs `devDependencies` (dev-only); commit `package-lock.json`.
- Never use sync `fs` methods in production servers (blocks the whole thread).
- Express middleware: `(req, res, next)` — must call `next()` or the request hangs.
- Error middleware: 4 params `(err, req, res, next)`, registered LAST.
- Auth: bcrypt-hash passwords, JWT for stateless auth, `httpOnly` cookies for XSS-resistance.

## Module 20 — Databases
- SQL = structured/relational (tables, JOINs); NoSQL = flexible/document-based.
- Transactions = all-or-nothing (Atomicity) — critical for multi-step operations (bank transfers).
- Parameterized queries ALWAYS — never string-concatenate user-input into SQL.
- Indexes speed up reads, slow down writes slightly — use strategically.

## Module 21-22 — Testing, Debugging, Performance
- Unit (isolated) → Integration (combined) → E2E (full app) testing.
- Mocks (fixed + verified) / Stubs (fixed only) / Spies (real + observed).
- Big O: watch for accidental O(n²) nested loops — Set/Map lookups are O(1).
- Debounce (wait for pause) vs Throttle (fixed interval) — different use-cases.
- Memory leaks: forgotten listeners, uncleared timers, accidental globals.

## Module 23 — Security
- XSS: use `textContent`, never `innerHTML` with untrusted input.
- CSRF: anti-CSRF tokens + `SameSite` cookies.
- SQL Injection: parameterized queries always.
- Broken Access Control: check OWNERSHIP, not just authentication.
- Never hardcode secrets — use environment variables, server-side only.

## Module 24-26 — Internals, Patterns, Professional
- Closures = function's `[[Environment]]` slot referencing outer Environment Record.
- Prototype lookup = `[[Get]]` traversing `[[Prototype]]` chain.
- Proxy intercepts operations; Reflect invokes default behavior.
- Design patterns: Factory (flexible creation), Singleton (one instance), Observer/Pub-Sub (decoupled events), Strategy (interchangeable algorithms).
- DRY + Single Responsibility + meaningful naming = professional code quality.

## Self-Check
- [ ] Can explain ESM vs CommonJS in one sentence each
- [ ] Can write Express middleware with proper `next()` usage
- [ ] Can explain why transactions matter with a real example
- [ ] Can list 4 OWASP-style security principles from memory

---

**Next:** [30-Revision/JavaScript-Gotchas.md](../JavaScript-Gotchas.md)
