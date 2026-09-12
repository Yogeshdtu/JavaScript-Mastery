# Output-Prediction Problems

**Module:** 28-Coding-Problems
**Previous:** [04-Debugging/01-Problems.md](../04-Debugging/01-Problems.md)
**Solutions:** [Solutions/05-Output-Prediction-Solutions.md](../Solutions/05-Output-Prediction-Solutions.md)

---

**Instructions:** Predict the exact output BEFORE running each snippet. Write your prediction down, then verify.

### Problem 1 — Hoisting
```js
console.log(a);
var a = 5;
console.log(b);
let b = 10;
```
**Concepts Tested:** Hoisting, TDZ (Module 02, chapter 03)

### Problem 2 — Closures
```js
function outer() {
  let count = 0;
  return [() => ++count, () => ++count];
}
const [inc1, inc2] = outer();
console.log(inc1());
console.log(inc2());
console.log(inc1());
```
**Concepts Tested:** Closures (Module 08, chapter 04)

### Problem 3 — `this`
```js
const obj = {
  name: "Test",
  regular: function () { return this.name; },
  arrow: () => { return this.name; }
};
console.log(obj.regular());
console.log(obj.arrow());
```
**Concepts Tested:** `this` binding, arrow functions (Module 08, chapter 05)

### Problem 4 — Type Coercion
```js
console.log(1 + "1");
console.log(1 - "1");
console.log("5" + 3 + 1);
console.log(5 + 3 + "1");
```
**Concepts Tested:** Implicit coercion (Module 09, chapter 02)

### Problem 5 — Event Loop
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```
**Concepts Tested:** Event loop, microtask vs macrotask (Module 15)

### Problem 6 — Array Reference
```js
const arr1 = [1, 2, 3];
const arr2 = arr1;
arr2.push(4);
console.log(arr1);
console.log(arr1 === arr2);
```
**Concepts Tested:** Reference types (Module 02, chapter 05)

### Problem 7 — Prototypes
```js
function Person(name) { this.name = name; }
Person.prototype.greet = function () { return `Hi, ${this.name}`; };
const p = new Person("Zoya");
console.log(p.greet());
console.log(p.hasOwnProperty("greet"));
console.log(p.hasOwnProperty("name"));
```
**Concepts Tested:** Prototypes (Module 10, chapter 03)

### Problem 8 — Async/Await Ordering
```js
async function test() {
  console.log("1");
  await null;
  console.log("2");
}
console.log("3");
test();
console.log("4");
```
**Concepts Tested:** Async/await, microtasks (Module 14, chapter 01; Module 15)

### Problem 9 — Equality Chain
```js
console.log(null == undefined);
console.log(null === undefined);
console.log(NaN == NaN);
console.log([1,2] == "1,2");
```
**Concepts Tested:** Equality (Module 09, chapter 03)

### Problem 10 — Destructuring Defaults
```js
function greet({ name = "Guest", age } = {}) {
  console.log(name, age);
}
greet();
greet({ name: "Aryan" });
greet({ age: 25 });
```
**Concepts Tested:** Destructuring, defaults (Module 07, chapter 02)

---

**Next:** [06-Interview-Level/01-Problems.md](../06-Interview-Level/01-Problems.md) | **Solutions:** [Solutions/05-Output-Prediction-Solutions.md](../Solutions/05-Output-Prediction-Solutions.md)
