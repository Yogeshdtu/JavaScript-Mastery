# Cheat Sheet: Operators

**Full detail:** [03-Operators/](../../03-Operators/)

---

## Equality
| Operator | Coercion? | Use |
|----------|-----------|-----|
| `==` ⚠️ | Yes | Avoid (except `== null`) |
| `===` ✅ | No | Always prefer |

## Logical / Nullish
```js
a || b   // returns b if a is FALSY (0, "", false, null, undefined, NaN)
a ?? b   // returns b only if a is null/undefined
a?.b     // safe property access — no error if a is null/undefined
a?.()    // safe function call
```

## Spread vs Rest
```js
[...arr]              // Spread — expands
function f(...args)   // Rest — collects
```

## Increment
```js
x++   // post: returns OLD value, then increments
++x   // pre: increments, then returns NEW value
```

## typeof / instanceof / in / delete
```js
typeof value          // primitive type check
value instanceof Cls  // prototype-chain check
"key" in obj           // existence check (own + inherited)
delete obj.key         // removes property
```

## Falsy Values (memorize all 8)
`false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`
**Everything else is truthy — including `[]` and `{}`!**

## Common Gotchas
- `[] == false` → `true` (coercion chain)
- `"" == "0"` → `false` (same type, no coercion, direct compare)
- Bitwise operators (`&`, `|`) ≠ logical (`&&`, `||`)

---

**Next:** [03-Arrays.md](./03-Arrays.md)
