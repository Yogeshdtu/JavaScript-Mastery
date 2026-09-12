# Function Basics — Declarations and Expressions

**Module:** 05-Functions
**Difficulty:** 🟢 Beginner
**Previous:** [04-Control-Flow/04-break-and-continue.md](../04-Control-Flow/04-break-and-continue.md)
**Next:** [02-Arrow-Functions.md](./02-Arrow-Functions.md)

---

## 1. Learning Objectives

- Function declaration aur function expression banana aur unka difference samajhna.
- Functions ko "first-class citizens" kehne ka matlab samajhna.
- Return values aur function ke bina return wala behavior samajhna.

## 2. Prerequisites

Modules 01-04 complete.

## 3. Concept in Simple Hinglish

Function ek **reusable block of code** hai jo ek specific task perform karta hai. Isse tum code ko ek jagah likh ke, jitni baar chahiye utni baar "call" kar sakte ho — bina usse repeat likhe. JavaScript mein functions ko banane ke multiple tareeke hain, aur functions khud values ki tarah treat hote hain (variables mein store, function ko function mein pass) — isi ko "first-class functions" kehte hain.

## 4. Technical Explanation

**Function Declaration:**
```js
function functionName(parameters) {
  // code
  return value;
}
```
Hoisted hota hai — declaration se pehle bhi call kar sakte ho (Module 02, chapter 03 se yaad karo).

**Function Expression:**
```js
const functionName = function (parameters) {
  return value;
};
```
Variable mein assign hota hai — hoisting behavior variable declaration (`var`/`let`/`const`) ke hisaab se hota hai, function definition khud hoist nahi hoti.

**Named Function Expression:**
```js
const factorial = function fact(n) {
  return n <= 1 ? 1 : n * fact(n - 1); // internal name 'fact' useful for recursion
};
```

## 5. Syntax

```js
function greet(name) {
  return `Hello, ${name}!`;
}

const greet2 = function (name) {
  return `Hi, ${name}!`;
};
```

## 6. Basic Examples

```js
function add(a, b) {
  return a + b;
}
console.log(add(3, 4)); // 7

const multiply = function (a, b) {
  return a * b;
};
console.log(multiply(3, 4)); // 12
```

## 7. Detailed Examples

**Return value ka importance:**
```js
function withReturn(a, b) {
  return a + b;
}

function withoutReturn(a, b) {
  a + b; // Calculation hoti hai, but return nahi hoti
}

console.log(withReturn(2, 3));    // 5
console.log(withoutReturn(2, 3)); // undefined ⚠️ — no return means undefined
```

Bina explicit `return` ke, function **always `undefined`** return karta hai — chahe andar calculation ho bhi rahi ho.

**Functions as values (first-class functions):**
```js
function sayHello() {
  return "Hello!";
}

// Function ko variable mein store karna
const myFunc = sayHello;
console.log(myFunc()); // "Hello!"

// Function ko dusre function mein pass karna
function executeFunction(fn) {
  return fn();
}
console.log(executeFunction(sayHello)); // "Hello!"
```
Ye "first-class functions" ka core idea hai — function ek normal value (jaise number ya string) ki tarah store aur pass ho sakta hai. Yahi property callbacks aur higher-order functions (agla chapter) ka foundation hai.

**Early return pattern:**
```js
function checkAge(age) {
  if (age < 0) {
    return "Invalid age"; // Early return
  }
  if (age < 18) {
    return "Minor";
  }
  return "Adult";
}

console.log(checkAge(-5)); // "Invalid age"
console.log(checkAge(15));  // "Minor"
console.log(checkAge(25));  // "Adult"
```

## 8. Mental Model

Function ko socho ek **coffee machine** ki tarah — tum input dete ho (parameters/beans+water), machine process karta hai (function body), aur output milta hai (return value/coffee). Same machine, jitni baar chalao, same process, but different inputs se different outputs.

## 9. What Happens Internally?

Jab function call hoti hai, engine ek **naya Execution Context** create karta hai (apna scope, apna `this`, apni memory). Function body execute hoti hai us context ke andar. `return` statement milte hi context destroy ho jaata hai (agar closure na bane — Module 05, chapter 06 mein detail) aur value caller ko wapas milti hai.

## 10. Common Mistakes

- `return` bhool jaana aur `undefined` expect na karna.
- Function declaration aur expression ke hoisting behavior ko confuse karna.
- Function ko call karna bhool jaana (`myFunc` likhna jab `myFunc()` chahiye tha) — pehla function reference deta hai, dusra actually call karta hai.

## 11. Edge Cases

```js
function multiReturn() {
  return 1;
  console.log("This never runs"); // Unreachable code — return ke baad kuch nahi chalta
}
```
`return` ke baad koi bhi code us function call mein **never execute** hota — engine turant function se exit ho jaata hai.

## 12. Real-World Usage

Functions har JavaScript application ka core building block hain — API calls handle karna, data process karna, UI update karna — sab functions ke through hota hai. Modular, reusable functions likhna professional code ki nishani hai.

## 13. Comparison With Related Concepts

| Aspect | Function Declaration | Function Expression |
|--------|------------------------|-------------------------|
| Hoisting | Fully hoisted | Depends on variable type (var/let/const) |
| Syntax | `function name() {}` | `const name = function() {}` |
| Can be anonymous? | No | Yes |

## 14. Practice Questions

1. Function declaration aur expression mein hoisting ka difference kya hai?
2. Bina `return` ke function kya return karta hai?
3. "First-class functions" ka matlab kya hai?

## 15. Challenge

Ek function `calculateArea(shape, dimension)` likho jo `"circle"` ke liye `π * r²` aur `"square"` ke liye `side²` calculate kare using `if-else`. Function expression syntax use karo.

## 16. Interview Questions

**Q1: Function declaration aur function expression mein kya difference hai?**
A: Function declaration (`function name() {}`) fully hoisted hoti hai — isliye declaration se pehle bhi call ki ja sakti hai. Function expression (`const name = function() {}`) ek variable ko function value assign karti hai — us variable ka hoisting behavior `var`/`let`/`const` ke rules follow karta hai, function definition khud hoist nahi hoti. Isliye function expression ko declaration se pehle call karna error dega.

**Q2: "First-class functions" ka JavaScript mein kya matlab hai?**
A: Matlab hai ki functions ko **normal values** ki tarah treat kiya ja sakta hai — variables mein assign kar sakte ho, dusre functions ko argument ki tarah pass kar sakte ho, aur functions se return bhi kar sakte ho. Ye property callbacks, higher-order functions, aur functional programming patterns ka foundation hai JavaScript mein.

## 17. Chapter Summary

Functions reusable code blocks hain jo declaration ya expression syntax se bante hain. Function declarations fully hoist hoti hain, expressions nahi. `return` statement function ka output decide karta hai — na hone pe `undefined` milta hai. Functions first-class citizens hain — values ki tarah store/pass ho sakte hain.

## 18. Revision Checklist

- [ ] Function declaration vs expression ka hoisting difference clear hai.
- [ ] `return` ki importance samajh gayi.
- [ ] First-class functions ka concept clear hai.

---

**Next:** [02-Arrow-Functions.md](./02-Arrow-Functions.md)
