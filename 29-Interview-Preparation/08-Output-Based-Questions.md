# Interview Prep: Output-Based Questions

**Module:** 29-Interview-Preparation
**Previous:** [07-NodeJS.md](./07-NodeJS.md)
**Next:** [09-Coding-Questions.md](./09-Coding-Questions.md)

---

Predict the output BEFORE reading the answer. (More output-prediction practice: [28-Coding-Problems/05-Output-Prediction/01-Problems.md](../28-Coding-Problems/05-Output-Prediction/01-Problems.md))

**Q1:**
```js
function foo() {
  console.log(this);
}
foo();
```
**Answer:** `undefined` (strict mode / ES modules) or the global object (`window`/`global`, non-strict mode) — default binding, since `foo()` is called as a plain function. (Detail: [08-Scope-and-Closures/05-this-Keyword.md](../08-Scope-and-Closures/05-this-Keyword.md))

**Q2:**
```js
console.log([1, 2, 3] + [4, 5]);
```
**Answer:** `"1,2,34,5"` — both arrays convert to strings via `toString()` (comma-joined), then concatenate. (Detail: [09-Type-Coercion/04-Object-to-Primitive-Conversion.md](../09-Type-Coercion/04-Object-to-Primitive-Conversion.md))

**Q3:**
```js
let x = 1;
function test() {
  console.log(x);
  let x = 2;
}
test();
```
**Answer:** `ReferenceError: Cannot access 'x' before initialization` — the inner `let x` shadows the outer `x` throughout the ENTIRE function scope, and it's in the TDZ at the point of `console.log`. (Detail: [02-Variables-and-Data-Types/03-Hoisting-and-TDZ.md](../02-Variables-and-Data-Types/03-Hoisting-and-TDZ.md))

**Q4:**
```js
const obj = { a: 1 };
Object.freeze(obj);
obj.a = 2;
console.log(obj.a);
```
**Answer:** `1` — `Object.freeze()` prevents the modification (silently in non-strict mode). (Detail: [07-Objects/03-Object-Methods.md](../07-Objects/03-Object-Methods.md))

**Q5:**
```js
console.log(typeof typeof 1);
```
**Answer:** `"string"` — `typeof 1` is `"number"` (a string), and `typeof "number"` is `"string"`.

**Q6:**
```js
const arr = [1, 2, 3];
arr.length = 1;
console.log(arr);
```
**Answer:** `[1]` — setting `.length` truncates the array. (Detail: [06-Arrays/01-Array-Basics.md](../06-Arrays/01-Array-Basics.md))

**Q7:**
```js
async function foo() {
  return 1;
}
foo().then(val => console.log(val));
console.log(2);
```
**Answer:** `2` then `1` — `foo()` returns a Promise (async functions always do), and `.then()` callbacks are microtasks, running after the synchronous `console.log(2)`. (Detail: [14-Promises-and-Async-Await/01-Async-Await-Basics.md](../14-Promises-and-Async-Await/01-Async-Await-Basics.md))

**Q8:**
```js
class Animal {
  speak() { return "generic sound"; }
}
class Dog extends Animal {}
console.log(new Dog().speak());
```
**Answer:** `"generic sound"` — `Dog` inherits `speak()` from `Animal.prototype` via the prototype chain, since `Dog` doesn't override it. (Detail: [10-Modern-JavaScript/02-Classes-and-Inheritance.md](../10-Modern-JavaScript/02-Classes-and-Inheritance.md))

**Q9:**
```js
console.log(0.1 + 0.2 === 0.3);
```
**Answer:** `false` — floating-point precision issue; `0.1 + 0.2` is actually `0.30000000000000004`. (Detail: [03-Operators/01-Arithmetic-and-Assignment-Operators.md](../03-Operators/01-Arithmetic-and-Assignment-Operators.md))

**Q10:**
```js
function outer() {
  const arr = [];
  for (let i = 0; i < 3; i++) {
    arr.push(() => i);
  }
  return arr.map(fn => fn());
}
console.log(outer());
```
**Answer:** `[0, 1, 2]` — `let` creates a new binding per iteration, so each closure captures its own `i`. (Compare to the `var` version, which would give `[3, 3, 3]` — Module 08, chapter 06)

---

**Next:** [09-Coding-Questions.md](./09-Coding-Questions.md)
