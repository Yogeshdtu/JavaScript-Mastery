# Quick Revision: Arrays, Objects, Scope, Coercion (Modules 06-09)

**Previous:** [01-Foundations-to-Functions.md](./01-Foundations-to-Functions.md)
**Next:** [03-Modern-JS-to-Async.md](./03-Modern-JS-to-Async.md)

---

## Module 06 — Arrays
- Mutating: `push`/`pop`/`shift`/`unshift`/`splice`/`sort`/`reverse`.
- Non-mutating: `slice`/`map`/`filter`/`reduce`/`find`/`some`/`every`/`includes`.
- `sort()` defaults to STRING comparison — always pass `(a,b) => a-b` for numbers.
- `forEach` returns `undefined` — use `map` when you need a new array.
- All array-copy methods are SHALLOW.

## Module 07 — Objects
- Dot notation for static keys, bracket notation for dynamic/special keys.
- Destructuring: `{ a, b: renamed, c = default, ...rest }`.
- `Object.freeze()` is shallow; `Object.assign()` mutates its first argument.
- Getters/setters look like properties but run functions.

## Module 08 — Scope and Closures
- Scope: inner sees outer, never the reverse. Lexical = defined by write-location.
- Closures persist outer-scope variables via the function's `[[Environment]]` link.
- `this`: 4 rules (new > explicit > implicit > default); arrows use lexical `this`.
- `call`/`apply` invoke immediately; `bind` returns a new function.

## Module 09 — Type Coercion
- 8 falsy values only; `[]` and `{}` are truthy.
- `+` prefers string concatenation if either side is a string; other math operators force numbers.
- `==` follows the Abstract Equality Algorithm — NOT transitive (`"" == 0` and `0 == "0"` but `"" != "0"`).
- `ToPrimitive` order: `Symbol.toPrimitive` > `valueOf` > `toString` (order reversed for "string" hint).

## Self-Check
- [ ] Can explain why `[10,2].sort()` gives `[10,2]`
- [ ] Can write object destructuring with renaming + defaults
- [ ] Can trace `[] == false` step-by-step
- [ ] Know why `==` is not transitive

---

**Next:** [03-Modern-JS-to-Async.md](./03-Modern-JS-to-Async.md)
