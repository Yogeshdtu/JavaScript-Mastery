# Cheat Sheet: Variables and Data Types

**Full detail:** [02-Variables-and-Data-Types/](../../02-Variables-and-Data-Types/)

---

## Declarations

| Keyword | Scope | Reassign? | Redeclare? | Hoisting |
|---------|-------|-----------|------------|----------|
| `var` ⚠️ | Function | ✅ | ✅ | `undefined` |
| `let` ✅ | Block | ✅ | ❌ | TDZ |
| `const` ✅ | Block | ❌ | ❌ | TDZ |

## Primitive Types (7)
`string`, `number`, `bigint`, `boolean`, `undefined`, `null`, `symbol`
- Copy by **value**, immutable.

## Reference Types
`object`, `array`, `function` (and more)
- Copy by **reference**, mutable.

## `null` vs `undefined`
- `undefined` = not yet assigned (JS default)
- `null` = explicitly "no value" (developer-set)
- `typeof null` → `"object"` (historic bug)
- `null == undefined` → `true`; `null === undefined` → `false`

## Type Checking
```js
typeof value          // primitives + functions
Array.isArray(value)  // arrays specifically
value === null        // null check
```

## Quick Gotchas
- `typeof []` → `"object"` (not `"array"`)
- `NaN === NaN` → `false` (use `Number.isNaN()`)
- `0.1 + 0.2 !== 0.3` (floating-point precision)

---

**Next:** [02-Operators.md](./02-Operators.md)
