# Cheat Sheet: Arrays

**Full detail:** [06-Arrays/](../../06-Arrays/)

---

## Mutating Methods ⚠️ (change original)
| Method | Action |
|--------|--------|
| `push(x)` | Add to end |
| `pop()` | Remove from end |
| `shift()` | Remove from start (O(n)) |
| `unshift(x)` | Add to start (O(n)) |
| `splice(start, count, ...items)` | Add/remove/replace anywhere |
| `sort(compareFn)` | Sort (default = string sort!) |
| `reverse()` | Reverse order |

## Non-Mutating Methods ✅ (return new)
| Method | Purpose |
|--------|---------|
| `slice(start, end)` | Extract portion |
| `map(fn)` | Transform each (same length) |
| `filter(fn)` | Select matching (subset) |
| `reduce(fn, initial)` | Combine into one value |
| `find(fn)` | First match (or `undefined`) |
| `findIndex(fn)` | Index of first match |
| `some(fn)` | At least one matches? |
| `every(fn)` | All match? |
| `includes(x)` | Contains value? |
| `flat(depth)` | Flatten nested arrays |
| `flatMap(fn)` | map + flat(1) |

## Critical Gotchas
```js
[10, 2, 33].sort();              // [10, 2, 33] — WRONG! Default sorts as strings
[10, 2, 33].sort((a,b) => a-b);  // [2, 10, 33] — Correct

[1,2,3].forEach(n => n*2);       // undefined — forEach doesn't return anything!
[1,2,3].map(n => n*2);           // [2,4,6] — map returns transformed array

let copy = [...original];        // Shallow copy — nested objects still shared!
```

## Removing Duplicates
```js
[...new Set(arr)]
```

---

**Next:** [04-Objects.md](./04-Objects.md)
