# CommonJS

**Module:** 17-Modules
**Difficulty:** 🟡 Intermediate
**Previous:** [01-ES-Modules.md](./01-ES-Modules.md)
**Next:** [03-ESM-vs-CommonJS-and-Dynamic-Imports.md](./03-ESM-vs-CommonJS-and-Dynamic-Imports.md)

---

## 1. Learning Objectives

- `require()` aur `module.exports` syntax samajhna.
- CommonJS ka synchronous-loading behavior samajhna.
- Ye samajhna ki CommonJS abhi bhi Node.js ecosystem mein kyun common hai.

## 2. Prerequisites

[01-ES-Modules.md](./01-ES-Modules.md)

## 3. Concept in Simple Hinglish

**⚠️ CommonJS** Node.js ka **original (legacy)** module system tha — ES Modules ke standardize hone se pehle. `require()` se files import hoti hain, `module.exports` se cheezein export hoti hain. Ye purane Node.js codebases aur bahut sari npm packages mein aaj bhi milega, isliye samajhna zaroori hai.

## 4. Technical Explanation

**`module.exports`:** Ek object hai jise ek file "export" karti hai — jo bhi is object pe assign kiya jaaye, wahi doosri files ko available hota hai `require()` ke through.

**`require()`:** Ek function jo synchronously kisi module ko load karta hai aur uska `module.exports` return karta hai.

## 5. Syntax

```js
// math.js
function add(a, b) { return a + b; }
module.exports = { add };
// or: module.exports.add = add;

// app.js
const { add } = require("./math.js");
```

## 6. Basic Examples

```js
// greet.js
function greet(name) {
  return `Hello, ${name}`;
}
module.exports = greet;

// app.js
const greet = require("./greet.js");
console.log(greet("Ishaan")); // "Hello, Ishaan"
```

## 7. Detailed Examples

**Exporting multiple values:**
```js
// mathUtils.js
function add(a, b) { return a + b; }
function subtract(a, b) { return a - b; }

module.exports = { add, subtract };
// Alternative syntax:
// exports.add = add;
// exports.subtract = subtract;

// app.js
const mathUtils = require("./mathUtils.js");
console.log(mathUtils.add(5, 3)); // 8

const { add, subtract } = require("./mathUtils.js"); // Destructuring also works
```

**⚠️ A common gotcha — `exports` vs `module.exports`:**
```js
// This works:
exports.add = function (a, b) { return a + b; };

// This does NOT work as expected:
exports = function (a, b) { return a + b; }; // ❌ Breaks the reference! require() won't get this.

// The correct way to export a SINGLE value/function:
module.exports = function (a, b) { return a + b; }; // ✅ Correct
```
`exports` sirf ek **shortcut reference** hai `module.exports` ka. Agar tum `exports` ko **reassign** karte ho (naya object/function assign), reference toot jaata hai — `require()` ab bhi original `module.exports` object hi return karega, tumhara naya assignment "lost" ho jaata hai. Isi liye single value export karne ke liye **hamesha `module.exports = ...` use karo**, `exports = ...` nahi.

**CommonJS module caching:**
```js
// counter.js
let count = 0;
module.exports = {
  increment() { return ++count; }
};

// app.js
const counter1 = require("./counter.js");
const counter2 = require("./counter.js"); // Same cached instance!

console.log(counter1.increment()); // 1
console.log(counter2.increment()); // 2 — because counter1 and counter2 are the SAME object!
```
`require()` modules ko **cache** karta hai — same file ko multiple baar `require` karne se wahi ek instance milta hai, naya nahi banta.

**Synchronous nature — a key CommonJS characteristic:**
```js
const fs = require("fs"); // Loads and executes IMMEDIATELY, synchronously
console.log("This runs after fs is fully loaded");
```
CommonJS `require()` **synchronous** hai — jab tak module poora load/execute na ho jaaye, agli line nahi chalti. Ye Node.js (server-side, file-system-based) ke liye theek hai, lekin browsers ke liye impractical hai (network se file lana synchronous nahi ho sakta bina blocking ke) — yahi ek major reason hai ES Modules (jo asynchronously load ho sakte hain) ka standard banna.

## 8. Mental Model

CommonJS ko socho ek **purane zamane ka library system** jaha har baar book (module) mangwane pe librarian (Node.js) turant jaake wo book laata hai (synchronous), tumhe wait karna padta hai. ES Modules ek "modern digital library" ki tarah hai jo books ko background mein prepare kar sakti hai (asynchronous).

## 9. What Happens Internally?

Jab `require("./file.js")` call hota hai, Node.js: (1) file ko find karta hai, (2) agar already cached hai, cached `module.exports` return kar deta hai, (3) nahi to file ko read karke ek function mein "wrap" karta hai (jisme `module`, `exports`, `require` locally available hote hain), (4) execute karta hai, (5) result ko cache karke return karta hai.

## 10. Common Mistakes

- `exports = ...` (reassignment) use karna jab `module.exports = ...` chahiye tha.
- CommonJS aur ES Modules syntax ko ek hi file mein mix karne ki koshish karna bina proper configuration ke — error deta hai.
- Module caching ko na samajhna aur assume karna ki har `require()` naya instance deta hai.

## 11. Edge Cases

```js
// circular dependency example (a.js requires b.js, which requires a.js)
// CommonJS handles this, but can result in "partial exports" being received —
// a well-known gotcha in complex Node.js applications.
```

## 12. Real-World Usage

⚠️ **Legacy but still extremely common:** Bahut saari npm packages, purane Node.js projects, aur configuration files (jaise `.eslintrc.js`, purana `webpack.config.js`) CommonJS use karte hain. Naye projects generally ES Modules prefer karte hain, lekin CommonJS samajhna zaroori hai kyunki tum isse regularly encounter karoge.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** ES Modules — naye projects ke liye.
**⚠️ Legacy / Common in Existing Codebases:** CommonJS — Node.js ka original system, purane code/packages mein prevalent. Node.js dono support karta hai (file extension `.mjs`/`.cjs` ya `package.json` ke `"type"` field se differentiate hota hai).

## 14. Practice Questions

1. `exports.x = y` aur `exports = y` mein kya difference hai?
2. `require()` synchronous hai ya asynchronous?
3. Module caching ka practical implication kya hai?

## 15. Challenge

Ek CommonJS module `logger.js` banao jisme `module.exports` se ek object export ho jisme `info(message)` aur `error(message)` methods hon (different console styling ke saath, jaise prefix `[INFO]`/`[ERROR]`). `app.js` mein require karke test karo.

## 16. Interview Questions

**Q1: CommonJS aur ES Modules mein kya fundamental difference hai?**
A: CommonJS (`require`/`module.exports`) **synchronous** module-loading system hai, originally Node.js ke liye design kiya gaya — modules turant, blocking-fashion load hote hain. ES Modules (`import`/`export`) JavaScript ka **official, standardized** system hai jo **asynchronously** load ho sakta hai (browsers ke liye zaroori hai), static analysis support karta hai (imports top-level, statically determinable), aur automatically strict mode mein hota hai. Modern Node.js dono support karta hai, lekin ES Modules future-direction hai.

**Q2: `exports.foo = bar` aur `exports = bar` mein kya difference hai CommonJS mein?**
A: `exports` `module.exports` ka ek reference/alias hai. `exports.foo = bar` us **same underlying object** ki property set karta hai jo `module.exports` bhi point karta hai — ye kaam karta hai. Lekin `exports = bar` (poori reassignment) `exports` variable ko ek **naye object** ki taraf point kar deta hai, jabki `module.exports` **abhi bhi purane object** ki taraf point karta hai — `require()` hamesha `module.exports` return karta hai, isliye ye reassignment "lost" ho jaata hai. Single value export karne ke liye hamesha `module.exports = bar` use karna chahiye.

## 17. Chapter Summary

CommonJS (⚠️ legacy) Node.js ka original, synchronous module system hai — `require()`/`module.exports`. Modules cached hote hain (same instance milta hai repeated requires pe). `exports = ...` (reassignment) ek common gotcha hai — `module.exports = ...` use karna safe hai. ES Modules is system ko largely replace kar rahe hain, but CommonJS samajhna zaroori hai existing codebases ke liye.

## 18. Revision Checklist

- [ ] `require()`/`module.exports` syntax yaad hai.
- [ ] `exports` reassignment gotcha clear hai.
- [ ] CommonJS ka synchronous-loading characteristic samajh gaya.

---

**Next:** [03-ESM-vs-CommonJS-and-Dynamic-Imports.md](./03-ESM-vs-CommonJS-and-Dynamic-Imports.md)
