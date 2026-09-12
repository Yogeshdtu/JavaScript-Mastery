# JavaScript Gotchas — The Complete Reference

**Module:** 30-Revision
**Previous:** [Quick-Revision/04-Backend-to-Advanced.md](./Quick-Revision/04-Backend-to-Advanced.md)
**Next:** [Final-Revision-Roadmap.md](./Final-Revision-Roadmap.md)

---

Ye file poore course ke sabse confusing JavaScript behaviors ko ek jagah collect karti hai — jab bhi koi "wait, ye kaise?" wala moment aaye, yaha check karo.

---

## 1. Type Coercion Gotchas

```js
console.log([] == false);        // true
console.log("" == 0);             // true
console.log("" == "0");           // false — SAME type, no coercion!
console.log(null == undefined);   // true
console.log(null == 0);            // false — null only equals undefined via ==
console.log(NaN === NaN);          // false — always!
```
**Full detail:** [09-Type-Coercion/03-Equality-Deep-Dive.md](../09-Type-Coercion/03-Equality-Deep-Dive.md)

## 2. `typeof` Quirks

```js
console.log(typeof null);         // "object" — historic bug
console.log(typeof []);            // "object" — use Array.isArray()
console.log(typeof NaN);           // "number"
console.log(typeof function(){});  // "function"
```

## 3. Array Sort Trap

```js
[10, 2, 33].sort();               // [10, 2, 33] — sorts as STRINGS!
[10, 2, 33].sort((a,b) => a-b);   // [2, 10, 33] — correct
```

## 4. The `var` Loop Bug

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// 3, 3, 3 — shared binding

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// 0, 1, 2 — new binding per iteration
```

## 5. `this` Losing Its Context

```js
const obj = { name: "X", greet() { return this.name; } };
const fn = obj.greet;
fn(); // undefined — 'this' lost when detached from obj
```

## 6. Floating-Point Precision

```js
console.log(0.1 + 0.2);           // 0.30000000000000004
console.log(0.1 + 0.2 === 0.3);   // false
```

## 7. `fetch()` Doesn't Reject on HTTP Errors

```js
fetch("/api/404-endpoint").then(res => {
  console.log(res.ok);     // false
  console.log(res.status);  // 404
  // But the .then() STILL runs — no automatic .catch()!
});
```

## 8. Object.freeze() is Shallow

```js
const obj = Object.freeze({ nested: { a: 1 } });
obj.nested.a = 2; // Works! Nested objects aren't frozen.
```

## 9. Reference vs Value Confusion

```js
const arr1 = [1, 2];
const arr2 = arr1;
arr2.push(3);
console.log(arr1); // [1, 2, 3] — same reference!
```

## 10. `return` + ASI Trap

```js
function getObj() {
  return
  { value: 1 };
}
console.log(getObj()); // undefined! ASI inserts a semicolon after 'return'
```

## 11. Default Parameters Only Trigger on `undefined`

```js
function greet(name = "Guest") { console.log(name); }
greet(undefined); // "Guest"
greet(null);       // null — default does NOT trigger!
```

## 12. `delete` on Arrays Creates Holes

```js
const arr = [1, 2, 3];
delete arr[1];
console.log(arr);        // [1, <1 empty item>, 3]
console.log(arr.length);  // 3 — unchanged!
```

## 13. Event Loop Priority Surprise

```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
// 1, 4, 3, 2 — microtasks ALWAYS beat 0ms macrotasks
```

## 14. `in` vs `hasOwnProperty`

```js
console.log("toString" in {});              // true — inherited!
console.log({}.hasOwnProperty("toString")); // false — not OWN
```

## 15. Closures in Loops Capture by Reference (with `var`)

Already covered in #4 — worth remembering as one of the most-asked interview questions.

## 16. Comparing Objects

```js
console.log({} === {});   // false — always, different references
console.log([] === []);    // false — same reason
```

## 17. `NaN` is the Only Value Not Equal to Itself

```js
console.log(NaN === NaN);          // false
console.log(Number.isNaN(NaN));     // true — the correct way to check
```

## 18. Arrow Functions Can't Be Constructors

```js
const Fn = () => {};
// new Fn(); // TypeError: Fn is not a constructor
```

## 19. String Immutability

```js
let str = "hello";
str[0] = "H"; // Silently does nothing (non-strict)
console.log(str); // "hello" — unchanged
```

## 20. Async Function Always Returns a Promise

```js
async function getValue() { return 42; }
console.log(getValue()); // Promise {<fulfilled>: 42} — NOT 42 directly!
```

---

**Next:** [Final-Revision-Roadmap.md](./Final-Revision-Roadmap.md)
