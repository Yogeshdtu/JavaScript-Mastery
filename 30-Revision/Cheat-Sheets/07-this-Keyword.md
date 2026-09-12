# Cheat Sheet: `this` Keyword

**Full detail:** [08-Scope-and-Closures/05-this-Keyword.md](../../08-Scope-and-Closures/05-this-Keyword.md), [06-call-apply-bind.md](../../08-Scope-and-Closures/06-call-apply-bind.md)

---

## Binding Rules (priority order, highest first)

| Priority | Rule | Example | `this` = |
|----------|------|---------|-----------|
| 1 | `new` binding | `new Fn()` | The new object |
| 2 | Explicit binding | `fn.call(obj)` | `obj` |
| 3 | Implicit binding | `obj.method()` | `obj` |
| 4 | Default binding | `fn()` | `undefined` (strict) / global |

**Arrow functions ignore all these rules** — they use lexical `this` (from enclosing scope), fixed at creation-time.

## call / apply / bind
```js
fn.call(thisArg, a, b);      // Calls immediately, args individually
fn.apply(thisArg, [a, b]);    // Calls immediately, args as array
const bound = fn.bind(thisArg); // Returns NEW function, doesn't call
```

## Classic Bug + Fix
```js
const obj = {
  name: "X",
  greet() {
    setTimeout(function() { console.log(this.name); }, 100); // undefined — lost 'this'
  }
};
// FIX:
const objFixed = {
  name: "X",
  greet() {
    setTimeout(() => console.log(this.name), 100); // "X" — arrow inherits lexical 'this'
  }
};
```

## Quick Test
```js
const person = { name: "A", greet() { return this.name; } };
const fn = person.greet;
fn(); // undefined — 'this' lost when extracted from object
```

---

**Next:** [08-Prototypes.md](./08-Prototypes.md)
