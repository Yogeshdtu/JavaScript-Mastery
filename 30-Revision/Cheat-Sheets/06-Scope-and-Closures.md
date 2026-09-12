# Cheat Sheet: Scope and Closures

**Full detail:** [08-Scope-and-Closures/](../../08-Scope-and-Closures/)

---

## Scope Types
- **Global** — accessible everywhere
- **Function** — `var` (and everything) contained within a function
- **Block** — `let`/`const` contained within `{}`

## Rule
Inner scope sees outer scope. Outer scope CANNOT see inner scope.

## Lexical Scoping
Scope is determined by WHERE a function is **written** (defined), not where it's called.

## Closure
```js
function outer() {
  let x = 10;
  return function inner() {
    return x; // 'inner' forms a closure over 'x'
  };
}
```
Closures persist as long as a reference to the inner function exists.

## The Classic `var`-in-loop Bug
```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // 3, 3, 3 (shared binding)
}
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // 0, 1, 2 (new binding per iteration)
}
```

## Execution Context
1. **Creation Phase** — hoisting happens here
2. **Execution Phase** — code actually runs

## Call Stack
LIFO. Deep/infinite recursion → Stack Overflow.

---

**Next:** [07-this-Keyword.md](./07-this-Keyword.md)
