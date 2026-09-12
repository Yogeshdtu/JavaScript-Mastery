# Callbacks and Higher-Order Functions

**Module:** 05-Functions
**Difficulty:** 🟡 Intermediate
**Previous:** [03-Parameters-Arguments-Defaults-Rest.md](./03-Parameters-Arguments-Defaults-Rest.md)
**Next:** [05-Recursion.md](./05-Recursion.md)

---

## 1. Learning Objectives

- Callback function ka concept samajhna.
- Higher-order functions kya hote hain, samajhna aur likhna.
- Callbacks ka async programming se connection samajhna (preview — Module 13 mein deep dive).

## 2. Prerequisites

Module 05, chapters 01-03. Section 1 of [01-Function-Basics.md](./01-Function-Basics.md) ka "first-class functions" concept especially important hai.

## 3. Concept in Simple Hinglish

**Callback** ek function hai jo dusre function ko **argument ki tarah pass** kiya jaata hai, taaki wo baad mein "call back" kiya ja sake. **Higher-order function** wo function hai jo **ya to callback accept karta hai, ya function return karta hai** (ya dono). Ye pattern JavaScript ki sabse powerful features mein se ek hai.

## 4. Technical Explanation

Function ko higher-order kehte hain agar wo:
1. Ek ya multiple functions ko **parameters** ki tarah accept kare, YA
2. Ek function ko **return** kare result ki tarah.

Callbacks do tarah ke hote hain:
- **Synchronous callbacks:** Turant execute hote hain (jaise `array.map(callback)`).
- **Asynchronous callbacks:** Baad mein execute hote hain (jaise `setTimeout(callback, 1000)`) — detail Module 13 mein.

## 5. Syntax

```js
function higherOrderFn(callback) {
  callback();
}

function returnsFunction() {
  return function () { /* ... */ };
}
```

## 6. Basic Examples

```js
function greet(name, callback) {
  console.log(`Hello, ${name}`);
  callback();
}

greet("Sneha", function () {
  console.log("Callback executed!");
});
// Hello, Sneha
// Callback executed!
```

## 7. Detailed Examples

**Array methods — the most common higher-order functions:**
```js
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(function (n) {
  return n * 2;
});
console.log(doubled); // [2, 4, 6, 8, 10]
```
`.map()` ek higher-order function hai — ye callback (function) ko parameter ki tarah accept karta hai aur har element pe usse call karta hai.

**Function returning a function (function factory):**
```js
function createMultiplier(factor) {
  return function (number) {
    return number * factor;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```
`createMultiplier` ek higher-order function hai kyunki ye function **return** karta hai. Ye pattern closures se connected hai (next chapter mein detail).

**Custom higher-order function — building your own:**
```js
function repeat(n, action) {
  for (let i = 0; i < n; i++) {
    action(i);
  }
}

repeat(3, function (index) {
  console.log(`Iteration ${index}`);
});
// Iteration 0
// Iteration 1
// Iteration 2
```

**Async callback preview — setTimeout:**
```js
console.log("Start");

setTimeout(function () {
  console.log("This runs later!");
}, 1000);

console.log("End");
// Output order: Start, End, This runs later! (after 1 second)
```
Ye async callback ka basic example hai — `setTimeout` immediately return ho jaata hai, callback baad mein queue hota hai. Full explanation [15-Event-Loop](../15-Event-Loop/) module mein hai.

## 8. Mental Model

Callback ko socho ek **instruction note jo tum kisi ko dete ho**: "jab tumhara kaam ho jaaye, ye specific step follow karna". Higher-order function ko socho ek **manager** jo apne kaam ke andar tumhare diye instructions (callback) ko sahi time pe execute karta hai.

## 9. What Happens Internally?

Jab callback function ko argument ki tarah pass kiya jaata hai, engine sirf function ka **reference** pass karta hai (functions reference types hain — Module 02, chapter 05). Higher-order function jab chahe (turant ya baad mein) us reference ko `()` laga ke call kar sakta hai.

## 10. Common Mistakes

- Callback ko **call** karne ke bajaye sirf **reference** pass karna bhool jaana, ya galti se `()` laga ke pass karna (jisse turant call ho jaata hai, expected time pe nahi):
```js
setTimeout(myFunction(), 1000);  // ❌ Wrong — myFunction() calls it immediately, passes its return value
setTimeout(myFunction, 1000);    // ✅ Correct — passes the function reference
```
- Bahut zyada nested callbacks likhna ("callback hell") — Module 13 mein solve kiya jaata hai Promises se.

## 11. Edge Cases

```js
function delayedGreeting(name, callback = () => {}) {
  callback(`Hello, ${name}`);
}
delayedGreeting("Test"); // No error — default empty callback handles missing callback
```
Default parameter (empty function) callback missing hone pe crash avoid karta hai — ek useful defensive pattern.

## 12. Real-World Usage

Callbacks event handling (`addEventListener`), array processing (`map`, `filter`, `reduce`), timers (`setTimeout`), aur (historically) async operations mein sab jagah hain. Higher-order functions functional programming style ka core hain — modern JavaScript codebases (React hooks, Redux) inhe extensively use karte hain.

## 13. Comparison With Related Concepts

| Concept | Relation |
|---------|----------|
| Callback | A function passed as an argument |
| Higher-order function | A function that accepts/returns functions |
| Closures (next chapter) | Often used together with HOFs (function factories) |
| Promises/async-await (Module 13-14) | Modern alternatives to callback-heavy async code |

## 14. Practice Questions

1. Higher-order function ki definition apne words mein likho.
2. `setTimeout(fn(), 1000)` aur `setTimeout(fn, 1000)` mein kya difference hai?
3. `.map()` ek higher-order function kyun hai?

## 15. Challenge

Ek higher-order function `createValidator(minLength)` likho jo return kare ek function jo check kare diya gaya string minLength se lamba hai ya nahi (return `true`/`false`).

## 16. Interview Questions

**Q1: Callback function kya hai, ek real-world example ke saath explain karo.**
A: Callback ek function hai jo argument ki tarah dusre function ko pass kiya jaata hai, taaki wo function usse appropriate time pe "call back" kar sake. Example: `array.map(callback)` mein `callback` har element ke liye call hota hai. `setTimeout(callback, 1000)` mein `callback` 1 second baad call hota hai. Callbacks JavaScript ko flexible aur event-driven banate hain — kyunki functions first-class values hain jo pass ki ja sakti hain.

**Q2: Higher-order function ke do examples do.**
A: (1) `Array.prototype.map()` — ek callback accept karta hai jo har array element pe apply hota hai, transformed array return karta hai. (2) Ek custom function factory jaise `function createMultiplier(x) { return (y) => x * y; }` — ye ek naya function **return** karta hai, isliye ye bhi higher-order function hai. Higher-order function ki definition ye hai ki wo function ko input leta hai, function ko output deta hai, ya dono.

## 17. Chapter Summary

Callback ek function hai jo dusre function ko argument ki tarah diya jaata hai, taaki wo appropriate time pe execute ho sake. Higher-order functions callbacks accept karte hain ya functions return karte hain — array methods, event handlers, aur function factories isi pattern pe based hain.

## 18. Revision Checklist

- [ ] Callback ka concept clear hai with real examples.
- [ ] Higher-order function ki definition yaad hai.
- [ ] Callback pass karne aur galti se call karne ka difference (`fn` vs `fn()`) samajh gaya.

---

**Next:** [05-Recursion.md](./05-Recursion.md)
