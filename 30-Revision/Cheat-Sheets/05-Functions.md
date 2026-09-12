# Cheat Sheet: Functions

**Full detail:** [05-Functions/](../../05-Functions/)

---

## Declaration vs Expression
```js
function name() {}          // Declaration — fully hoisted
const name = function() {}  // Expression — hoisting follows var/let/const rules
const name = () => {}       // Arrow function expression
```

## Arrow Functions vs Regular
| Feature | Regular | Arrow |
|---------|---------|-------|
| Own `this` | Yes (dynamic) | No (lexical) |
| `arguments` object | Yes | No |
| Can be constructor | Yes | No |
| Best for | Methods, constructors | Callbacks, preserving `this` |

## Parameters
```js
function f(a, b = 10, ...rest) {} // default + rest params
```
- Default triggers ONLY on `undefined`, not `null`.

## Closures
```js
function makeCounter() {
  let count = 0;
  return () => ++count; // Closure — remembers 'count'
}
```

## Pure Function Checklist
- ✅ Same input → same output
- ✅ No side effects (no external state modification)

## Recursion
- Needs a **base case** (stop condition) + **recursive case**.
- Missing base case → `RangeError: Maximum call stack size exceeded`.

## Critical Gotchas
```js
setTimeout(myFunction(), 1000);  // ❌ Calls immediately, passes return value
setTimeout(myFunction, 1000);    // ✅ Passes the function reference
```

---

**Next:** [06-Scope-and-Closures.md](./06-Scope-and-Closures.md)
