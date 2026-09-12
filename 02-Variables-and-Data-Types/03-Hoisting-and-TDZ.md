# Hoisting and Temporal Dead Zone (TDZ)

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [02-var-let-const.md](./02-var-let-const.md)
**Next:** [04-Primitive-Types.md](./04-Primitive-Types.md)

---

## 1. Learning Objectives

- Hoisting ka concept clearly samajhna.
- `var`, `let`, `const`, aur function declarations ka hoisting behavior compare karna.
- Temporal Dead Zone (TDZ) ka exact mechanism samajhna.

## 2. Prerequisites

[02-var-let-const.md](./02-var-let-const.md)

## 3. Concept in Simple Hinglish

**Hoisting** ka matlab hai — JavaScript engine code run karne se pehle, saari variable aur function declarations ko "upar utha ke" memory mein register kar leta hai (jaise ek pre-registration). Isi wajah se kuch variables code mein "upar" use karne pe bhi kaam kar jaate hain — lekin `let`/`const` ke saath ek trap hai jise **Temporal Dead Zone (TDZ)** kehte hain.

## 4. Technical Explanation

Engine code execute karne se pehle ek **Creation Phase** run karta hai (Module 08 mein "Execution Context" ke saath detail milega). Is phase mein:

- `var` declarations hoist hoti hain aur `undefined` se initialize ho jaati hain.
- `function` declarations **poori** hoist hoti hain (naam + body dono) — isliye function ko declaration se pehle call kar sakte ho.
- `let`/`const` declarations hoist hoti hain lekin **initialize nahi hoti** — wo ek "dead zone" mein rehte hain jab tak actual declaration line execute na ho.

**TDZ** = wo zone jaha `let`/`const` variable exist karta hai memory mein, lekin access karna error deta hai.

## 5. Syntax

```js
console.log(hoistedVar); // undefined (var hoisting)
var hoistedVar = 5;

console.log(hoistedLet); // ReferenceError (TDZ)
let hoistedLet = 5;
```

## 6. Basic Examples

```js
sayHello(); // "Hello!" — function declaration fully hoisted

function sayHello() {
  console.log("Hello!");
}
```

## 7. Detailed Examples

```js
console.log(a); // undefined
var a = 10;

console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 20;
```

**Kyun different behavior?**
- `a` (var) hoisting ke time `undefined` se initialize ho gaya, isliye `console.log(a)` error nahi deta, `undefined` print karta hai.
- `b` (let) hoist to hua, lekin uninitialized reh gaya (TDZ mein) — access karne pe engine turant error deta hai, taaki tumhe pata chale ki declaration se pehle use ki koshish ho rahi hai.

**Function Expressions vs Function Declarations:**
```js
sayHi(); // ✅ works — function declaration
function sayHi() { console.log("Hi"); }

sayBye(); // ❌ TypeError: sayBye is not a function
var sayBye = function () { console.log("Bye"); };
```

`sayBye` ek variable hai jisme function expression assign ho raha hai. `var` hoisting ki wajah se `sayBye` pehle `undefined` hai — usse function ki tarah call karna `TypeError` deta hai.

## 8. Mental Model

Socho JavaScript engine ek **guest list** pehle se bana leta hai (hoisting), lekin `var` guests turant "not arrived yet, seat khaali hai" (`undefined`) declare ho jaate hain, jabki `let`/`const` guests list mein hain lekin "arrival tak entry allowed nahi" (TDZ) — jab tak unki actual line execute nahi hoti.

## 9. What Happens Internally?

JavaScript ka execution do phases mein hota hai:
1. **Creation Phase (Memory allocation):** Engine saari declarations scan karta hai — `var` ko `undefined` assign, function declarations ko fully define, `let`/`const` ko "uninitialized" state mein rakhta hai.
2. **Execution Phase:** Code line-by-line chalta hai, values actually assign hoti hain.

TDZ basically wo gap hai Creation Phase mein variable "register" hone aur Execution Phase mein us line tak pahunchne ke beech.

## 10. Common Mistakes

- Sochna ki `let`/`const` hoist nahi hote — wo hoist hote hain, bas usable nahi hote TDZ ki wajah se (ye subtle difference interview mein pucha jaata hai).
- Function expressions ko function declarations ki tarah treat karna aur unhe declaration se pehle call karne ki koshish karna.

## 11. Edge Cases

```js
let x = 10;
function test() {
  console.log(x); // ReferenceError (TDZ), NOT 10!
  let x = 20;
}
test();
```
Yahan andar wali `let x` outer `x` ko "shadow" karti hai poore function scope mein — isliye `console.log(x)` outer `x` (10) access nahi karta, balki apne hi TDZ mein fasa hua hai.

## 12. Real-World Usage

Modern linters (ESLint rule `no-use-before-define`) hoisting-related bugs ko development time hi pakad lete hain. TDZ samajhna especially useful hai jab tum complex nested scopes ya modules debug kar rahe ho.

## 13. Comparison With Related Concepts

| Declaration Type | Hoisted? | Initial Value | Usable Before Declaration Line? |
|-------------------|----------|----------------|-----------------------------------|
| `var` | Yes | `undefined` | Yes (but value is `undefined`) |
| `let` / `const` | Yes | Uninitialized (TDZ) | No — throws ReferenceError |
| `function` declaration | Yes | Full function | Yes |
| `function` expression (via `var`/`let`) | Depends on variable type | `undefined` or TDZ | No (as a callable function) |

## 14. Practice Questions

1. `console.log(x); let x = 5;` — kya output aayega?
2. Function declaration aur function expression ke hoisting behavior mein kya difference hai?
3. TDZ ka full form kya hai aur ye kab start/end hota hai?

## 15. Challenge

Predict output, phir explain karo line-by-line:
```js
console.log(typeof myFunc);
console.log(typeof myVar);

var myVar = "value";
function myFunc() {}
```

## 16. Interview Questions

**Q1: Kya `let` aur `const` hoist hote hain?**
A: Haan, technically hoist hote hain — engine unhe Creation Phase mein register kar leta hai. Lekin unhe `undefined` se initialize nahi kiya jaata, balki wo Temporal Dead Zone (TDZ) mein rehte hain. Jab tak actual declaration line execute nahi hoti, unhe access karna `ReferenceError` deta hai. Isliye "hoisted but not initialized" sabse accurate answer hai.

**Q2: TDZ (Temporal Dead Zone) kya hai?**
A: TDZ wo period hai scope ke start hone se lekar `let`/`const` variable ki actual declaration line execute hone tak. Is period mein variable memory mein exist karta hai lekin access karne pe `ReferenceError` throw hota hai. Ye JavaScript ko `var` ke silent `undefined` bugs se safer banata hai.

## 17. Chapter Summary

Hoisting matlab declarations ko memory mein pre-register karna execution se pehle. `var` `undefined` se initialize hoti hai, function declarations fully hoisted hoti hain, aur `let`/`const` TDZ mein rehte hain jab tak unki line execute na ho — access karne pe error milta hai.

## 18. Revision Checklist

- [ ] Hoisting ka basic mechanism samajh gaya.
- [ ] `var` vs `let`/`const` hoisting behavior clear hai.
- [ ] TDZ exactly kya hai, explain kar sakta hoon.

---

**Next:** [04-Primitive-Types.md](./04-Primitive-Types.md)
