# Scope Types — Global, Function, Block

**Module:** 08-Scope-and-Closures
**Difficulty:** 🟡 Intermediate
**Previous:** [07-Objects/04-Property-Descriptors.md](../07-Objects/04-Property-Descriptors.md)
**Next:** [02-Lexical-Scope-and-Scope-Chain.md](./02-Lexical-Scope-and-Scope-Chain.md)

---

## 1. Learning Objectives

- Global, function, aur block scope ke beech difference samajhna.
- Variable "visibility rules" clearly samajhna.
- Scope-related common bugs identify karna.

## 2. Prerequisites

Modules 01-07, especially [02-Variables-and-Data-Types/02-var-let-const.md](../02-Variables-and-Data-Types/02-var-let-const.md).

## 3. Concept in Simple Hinglish

**Scope** decide karta hai ki ek variable **kaha kaha accessible hai** code mein. Socho scope ko ek "boundary/area" ki tarah — jaise ghar ke andar ka area (block scope), pura ghar (function scope), aur pura mohalla (global scope). Kisi bhi "andar" wale area se bahar ki cheezein dekhi ja sakti hain, lekin bahar se andar ki private cheezein nahi.

## 4. Technical Explanation

**Global Scope:** Sabse bahar ka scope. Yaha declared variables kisi bhi jagah access ho sakte hain poore program mein.

**Function Scope:** Function ke andar declared variables (`var`, `let`, `const` — sab) sirf **usi function ke andar** accessible hain.

**Block Scope:** `{ }` ke andar `let`/`const` se declared variables sirf **usi block ke andar** accessible hain (`if`, `for`, `while`, ya standalone `{ }`). `var` block scope follow **nahi** karta (Module 02, chapter 02 revise karo).

## 5. Syntax

```js
let globalVar = "I'm global"; // Global scope

function myFunction() {
  let functionVar = "I'm function-scoped"; // Function scope

  if (true) {
    let blockVar = "I'm block-scoped"; // Block scope
  }
  // blockVar not accessible here
}
```

## 6. Basic Examples

```js
let outside = "global";

function test() {
  let inside = "function-level";
  console.log(outside); // ✅ accessible — inner scope can see outer scope
  console.log(inside);   // ✅ accessible
}

test();
console.log(outside); // ✅ accessible
// console.log(inside); // ❌ ReferenceError — outer scope CANNOT see inner scope
```

## 7. Detailed Examples

**The core rule — inner sees outer, outer cannot see inner:**
```js
let a = "global A";

function outerFn() {
  let b = "outer B";

  function innerFn() {
    let c = "inner C";
    console.log(a, b, c); // ✅ All accessible — innermost function sees everything above it
  }

  innerFn();
  // console.log(c); // ❌ ReferenceError — outerFn cannot see innerFn's variables
}

outerFn();
```

**Block scope with `let`/`const` — proper containment:**
```js
if (true) {
  let x = "inside if block";
  const y = "also inside";
  var z = "I escape the block!"; // var ignores block scope
}

// console.log(x); // ❌ ReferenceError
// console.log(y); // ❌ ReferenceError
console.log(z);      // ✅ "I escape the block!" — var leaked out
```

**Global scope pollution — a real-world problem:**
```js
// ⚠️ Bad practice — accidentally creating global variables
function riskyFunction() {
  accidentalGlobal = "oops"; // No let/const/var — becomes global (non-strict mode)!
}
riskyFunction();
console.log(accidentalGlobal); // "oops" — leaked into global scope!
```
Ye Module 01, chapter 05 ke strict mode discussion se directly connected hai — strict mode is bug ko error mein convert kar deta hai.

**Nested block scopes:**
```js
{
  let level1 = "outer block";
  {
    let level2 = "inner block";
    console.log(level1, level2); // Both accessible
  }
  // console.log(level2); // ❌ ReferenceError
}
```

## 8. Mental Model

Scope ko socho **nested boxes** ki tarah — sabse bada box "Global" hai, uske andar "Function" boxes hain, unke andar "Block" boxes ho sakte hain. Ek chhota box (inner scope) apne saare bade boxes (outer scopes) ke andar ki cheezein dekh sakta hai, lekin bade box andar ke chhote boxes ki private cheezein nahi dekh sakte.

## 9. What Happens Internally?

Har scope ek **Environment Record** create karta hai (Module 08, chapter 03 mein detail) jo us scope ki variables ko store karta hai. Jab tum kisi variable ko access karte ho, engine current scope se lekar upar tak (outward) check karta hai jab tak variable milta nahi — ye process "scope chain" hai (next chapter).

## 10. Common Mistakes

- Strict mode use na karke accidentally global variables create karna.
- `var` ke block-scope-na-follow-karne wale behavior ko `let`/`const` jaisa expect karna.
- Global scope mein bahut saare variables declare karna — naming conflicts aur maintenance issues create karta hai.

## 11. Edge Cases

```js
function example() {
  console.log(typeof someGlobal); // "undefined" — safe check without ReferenceError
}
example();
```
`typeof` undeclared variables ko safely check karne deta hai bina error diye — direct access (`console.log(someGlobal)`) `ReferenceError` dega agar variable exist nahi karta kahi bhi.

## 12. Real-World Usage

Modern JavaScript modules (Module 17) automatically har file ko apna scope dete hain — global scope pollution avoid karne ka best modern approach. Block scoping ke saath `let`/`const` loops, conditionals mein clean, predictable variable containment deta hai — professional code mein isi liye `var` avoid kiya jaata hai.

## 13. Comparison With Related Concepts

| Scope Type | Created By | Variables Escape? |
|------------|--------------|----------------------|
| Global | Top-level code | N/A (already outermost) |
| Function | `function() {}` | No (any declaration type) |
| Block | `{}`, `if`, `for`, `while` | Only `var` escapes; `let`/`const` don't |

## 14. Practice Questions

1. `var` ke saath block scope kyun kaam nahi karta?
2. Global scope pollution kaise accidentally hoti hai?
3. Nested function scope mein variable access ka rule kya hai (inner→outer ya outer→inner)?

## 15. Challenge

Ek code snippet likho jisme 3 levels ki nesting ho (global → function → block), har level mein ek variable declare ho, aur innermost level se sabhi 3 variables ko successfully access karke print karo.

## 16. Interview Questions

**Q1: JavaScript mein scope ke kitne types hain aur unka basic rule kya hai?**
A: JavaScript mein 3 main scope types hain: **Global** (poore program mein accessible), **Function** (sirf us function ke andar), aur **Block** (sirf `{}` ke andar, `let`/`const` ke liye — `var` isse ignore karta hai). Core rule ye hai ki **inner scope outer scope ki variables dekh sakta hai, lekin outer scope inner scope ki variables nahi dekh sakta** — ye ek one-directional visibility hai.

**Q2: `var` block scope follow kyun nahi karta?**
A: `var` historically (pre-ES6) JavaScript ka sirf declaration type tha, aur us time language mein block-scoping concept exist nahi karta tha — sirf function-scoping thi. ES6 mein `let`/`const` introduce hue jo proper block-scoping support karte hain, lekin `var` backward-compatibility ke liye apna original function-scoped behavior retain karta hai. Isi liye `var` ko modern code mein avoid karne ki recommendation hai.

## 17. Chapter Summary

Scope teen levels mein organize hota hai: Global, Function, Block. Inner scopes outer scopes ki variables access kar sakte hain, opposite nahi. `let`/`const` proper block-scoping follow karte hain, `var` sirf function-scoped hai — blocks se "leak" ho jaata hai.

## 18. Revision Checklist

- [ ] Teeno scope types ka clear mental model hai.
- [ ] `var` ka block-scope-leak behavior yaad hai.
- [ ] Inner-to-outer visibility rule samajh gaya.

---

**Next:** [02-Lexical-Scope-and-Scope-Chain.md](./02-Lexical-Scope-and-Scope-Chain.md)
