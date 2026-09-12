# Quick Revision: Foundations to Functions (Modules 01-05)

**Previous:** [Cheat-Sheets/13-NodeJS.md](../Cheat-Sheets/13-NodeJS.md)
**Next:** [02-Arrays-Objects-Scope-Coercion.md](./02-Arrays-Objects-Scope-Coercion.md)

---

## Module 01 — Foundations
- JavaScript = dynamically-typed, multi-paradigm language, ECMAScript-standardized.
- Engines (V8) parse → interpret (Ignition) → JIT-optimize (TurboFan) hot code.
- Strict mode (`"use strict"`) catches silent errors — auto-on in classes/modules.
- ASI can break `return` statements split across lines.

## Module 02 — Variables and Data Types
- `let`/`const` (✅) over `var` (⚠️) — block-scoped, TDZ-protected.
- 7 primitives (immutable, copy-by-value) vs reference types (mutable, copy-by-reference).
- `typeof null === "object"` (bug), `NaN !== NaN`, arrays are objects (`typeof [] === "object"`).

## Module 03 — Operators
- `===` over `==` always (except `== null` for null/undefined check).
- `??` for defaults when `0`/`""` are valid; `?.` for safe nested access.
- Spread expands, Rest collects — same `...` syntax, opposite direction.

## Module 04 — Control Flow
- `switch` uses strict equality, needs `break` to avoid fall-through.
- `for...of` = values (arrays); `for...in` = keys (objects, avoid for arrays).
- `break` exits loop entirely; `continue` skips to next iteration.

## Module 05 — Functions
- Function declarations hoist fully; expressions follow variable-hoisting rules.
- Arrow functions: no own `this`, no `arguments`, not constructable — great for callbacks.
- Closures = inner function + reference to outer scope, persists after outer returns.
- Recursion needs a base case; missing one → stack overflow.
- Pure functions: same input → same output, no side effects.

## Self-Check
- [ ] Can explain hoisting for `var`/`let`/function-declarations
- [ ] Can write a closure-based counter from memory
- [ ] Know all 8 falsy values
- [ ] Can explain `for...of` vs `for...in` difference

---

**Next:** [02-Arrays-Objects-Scope-Coercion.md](./02-Arrays-Objects-Scope-Coercion.md)
