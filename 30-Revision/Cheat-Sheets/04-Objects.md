# Cheat Sheet: Objects

**Full detail:** [07-Objects/](../../07-Objects/)

---

## Access Patterns
```js
obj.key           // dot notation (static keys)
obj["key"]        // bracket notation (dynamic keys, special chars)
obj?.key          // optional chaining (safe access)
```

## Destructuring
```js
const { a, b: renamed, c = "default" } = obj;
const { x, ...rest } = obj; // rest collects remaining
```

## Spread / Merge
```js
const merged = { ...obj1, ...obj2 }; // later keys win on conflict
```

## Static Methods
| Method | Returns |
|--------|---------|
| `Object.keys(obj)` | Array of keys |
| `Object.values(obj)` | Array of values |
| `Object.entries(obj)` | Array of `[key, value]` pairs |
| `Object.assign(target, ...src)` | Merges (⚠️ mutates target!) |
| `Object.freeze(obj)` | Shallow-immutable |
| `Object.seal(obj)` | No add/delete, modify OK |

## Property Descriptors
```js
Object.defineProperty(obj, "key", { value, writable, enumerable, configurable });
```

## Getters/Setters
```js
const obj = {
  get area() { return this.w * this.h; },
  set area(v) { /* custom logic */ }
};
```

## Critical Gotchas
- `Object.freeze()` is **shallow** — nested objects still mutable.
- `{} === {}` → `false` (reference comparison, not content).
- Object keys are always **strings** (or Symbols), even if written as numbers.

---

**Next:** [05-Functions.md](./05-Functions.md)
