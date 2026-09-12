# IIFE, Function Composition, Pure Functions

**Module:** 05-Functions
**Difficulty:** 🟠 Intermediate
**Previous:** [06-Closures.md](./06-Closures.md)
**Next:** [06-Arrays/01-Array-Basics.md](../06-Arrays/01-Array-Basics.md)

---

## 1. Learning Objectives

- IIFE (Immediately Invoked Function Expression) ka purpose aur syntax samajhna.
- Function composition se chhote functions ko combine karna.
- Pure functions aur side effects ka concept clear karna.

## 2. Prerequisites

Module 05, chapters 01-06.

## 3. Concept in Simple Hinglish

**IIFE** ek function hai jo define hone ke **turant baad automatically call ho jaata hai** — ek baar use ho ke khatam. **Function composition** matlab chhote functions ko combine karke ek bada function banana. **Pure function** ek aisa function hai jo same input ke liye **hamesha same output** deta hai aur bahar ki kisi cheez ko affect (side effect) nahi karta.

## 4. Technical Explanation

**IIFE Syntax:**
```js
(function () {
  // code runs immediately
})();
```
Function ko parentheses mein wrap karna use ek **expression** banata hai (statement nahi), aur trailing `()` usse turant call kar deta hai.

**Pure Function Rules:**
1. Same input → same output, always.
2. No side effects (koi external state modify nahi karta — global variables, DOM, files, console.log bhi technically side effect hai).

**Function Composition:** Multiple functions ko combine karke ek pipeline banana, jaha ek function ka output dusre ka input banta hai.

## 5. Syntax

```js
// IIFE
(function () { console.log("Runs immediately"); })();
(() => { console.log("Arrow IIFE"); })();

// Composition
const compose = (f, g) => (x) => f(g(x));
```

## 6. Basic Examples

```js
(function () {
  let privateVar = "I'm private";
  console.log(privateVar);
})();
// "I'm private" — printed immediately, privateVar not accessible outside
```

## 7. Detailed Examples

**IIFE — historical use case (avoiding global scope pollution):**
```js
// ⚠️ Before ES6 modules, IIFE was THE way to avoid polluting global scope
var myModule = (function () {
  let privateCounter = 0;

  return {
    increment() {
      privateCounter++;
      return privateCounter;
    }
  };
})();

console.log(myModule.increment()); // 1
console.log(myModule.increment()); // 2
console.log(privateCounter);        // ❌ ReferenceError — truly private!
```
**✅ Modern equivalent:** Ye same encapsulation aaj **ES Modules** (Module 17) se milta hai — har module ki apni scope hoti hai automatically, IIFE ki zaroorat nahi rehti. IIFE samajhna zaroori hai kyunki purani libraries aur bundled code mein ye pattern bahut milega.

**Pure vs Impure functions:**
```js
// ✅ Pure function
function add(a, b) {
  return a + b; // Same inputs always give same output, no external state touched
}

// ❌ Impure function — depends on external state
let taxRate = 0.18;
function calculateTotalImpure(price) {
  return price + price * taxRate; // Depends on external 'taxRate' — result can change!
}

// ❌ Impure function — has a side effect
let total = 0;
function addToTotalImpure(amount) {
  total += amount; // Modifies external state
  return total;
}
```

**Function composition — building a data pipeline:**
```js
const trim = (str) => str.trim();
const toLowerCase = (str) => str.toLowerCase();
const removeSpaces = (str) => str.replace(/\s+/g, "-");

// Composing functions manually
function slugify(str) {
  return removeSpaces(toLowerCase(trim(str)));
}
console.log(slugify("  Hello World  ")); // "hello-world"

// Generic compose utility
const compose = (...fns) => (input) => fns.reduceRight((acc, fn) => fn(acc), input);
const slugify2 = compose(removeSpaces, toLowerCase, trim);
console.log(slugify2("  Hello World  ")); // "hello-world"
```
`compose` chhote, single-purpose functions (`trim`, `toLowerCase`, `removeSpaces`) ko ek pipeline mein combine karta hai — ye functional programming ka core idea hai: chhote reusable pieces se complex logic banana.

## 8. Mental Model

IIFE ko socho ek **firecracker** ki tarah — banate hi turant "phat" (execute) jaata hai, dobara use nahi hota. Pure function ko socho ek **calculator** — same buttons press karo, hamesha same result milega, calculator ke bahar kuch affect nahi hota. Function composition ko socho ek **assembly line** — har station (function) apna kaam karta hai, product (data) ko aage badhata hai.

## 9. What Happens Internally?

IIFE ek normal function call hi hai, sirf syntax trick hai jo function ko define aur call turant same expression mein karta hai — koi special engine behavior nahi. Pure functions engine-level pe kuch special nahi karte, lekin unki predictability compiler optimizations (memoization, jaise Module 22 mein) ko aasan banati hai kyunki result cache kiya ja sakta hai safely.

## 10. Common Mistakes

- IIFE ko modern code mein unnecessarily use karna jaha ES modules better fit hain.
- Function ko "pure" samajhna jabki wo `console.log` ya external variable modify kar raha ho — console.log bhi technically ek side effect hai.
- Composition ko itna deep bana dena ki debugging difficult ho jaaye — readability balance karo.

## 11. Edge Cases

```js
// IIFE with parameters
(function (name) {
  console.log(`Hello, ${name}`);
})("World"); // "Hello, World"
```

## 12. Real-World Usage

Pure functions Redux reducers, React state updates, aur unit-testable business logic mein highly preferred hain — predictable hone ki wajah se test karna aasan hota hai. Function composition utility libraries (Lodash/fp, Ramda) aur data-transformation pipelines mein common hai.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** ES Modules (encapsulation ke liye), pure functions jaha possible ho.
**⚠️ Legacy:** IIFE-based module pattern — purane codebases/bundled libraries mein milega, samajhna zaroori hai.

## 14. Practice Questions

1. IIFE ka main historical purpose kya tha?
2. Pure function ki 2 conditions kya hain?
3. `console.log` ko side-effect kyun mana jaata hai?

## 15. Challenge

3 pure functions likho: `capitalize(str)`, `addExclamation(str)`, `repeat(str, n)`. Phir ek `compose` function use karke inhe combine karke ek "shout" pipeline banao jo `"hello"` ko `"HELLO!HELLO!"` (n=2 case) jaisa output de.

## 16. Interview Questions

**Q1: Pure function kya hai aur ye important kyun hai?**
A: Pure function wo function hai jo (1) same arguments ke liye hamesha same result deta hai, aur (2) koi side effect nahi produce karta (external variables, DOM, files modify nahi karta). Ye important hai kyunki pure functions **predictable, testable, aur debug karne mein easy** hote hain — inka behavior sirf unke inputs pe depend karta hai, kisi hidden external state pe nahi. React aur Redux jaisi libraries pure functions ko heavily rely karti hain predictable state management ke liye.

**Q2: IIFE (Immediately Invoked Function Expression) kya hai aur ye kyun use hota tha?**
A: IIFE ek function hai jo define hote hi turant call ho jaata hai — syntax `(function(){...})()`. Historically (ES6 modules se pehle), IIFE ka main use tha global scope ko pollute na karna aur "private" variables create karna, kyunki function apna khud ka scope banata hai jo bahar se accessible nahi hota. Modern JavaScript mein ES Modules ne is need ko largely replace kar diya hai, lekin IIFE abhi bhi library-bundling aur specific initialization patterns mein dikhta hai.

## 17. Chapter Summary

IIFE ek function hai jo turant execute hota hai — historically scope-isolation ke liye use hua, aaj ES Modules ne largely replace kar diya hai. Pure functions predictable aur side-effect-free hote hain, testing/debugging easy banate hain. Function composition chhote functions ko pipeline mein combine karke complex logic banata hai.

## 18. Revision Checklist

- [ ] IIFE syntax aur historical purpose clear hai.
- [ ] Pure vs impure function ka difference example se samajh gaya.
- [ ] Function composition ka basic pattern likh sakta hoon.

---

**Module 05 Complete!** Next Module: [06-Arrays/01-Array-Basics.md](../06-Arrays/01-Array-Basics.md)
