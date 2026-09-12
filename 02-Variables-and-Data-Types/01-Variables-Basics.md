# Variables Basics

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟢 Beginner
**Previous:** [01-JavaScript-Foundations/06-Errors-and-ASI.md](../01-JavaScript-Foundations/06-Errors-and-ASI.md)
**Next:** [02-var-let-const.md](./02-var-let-const.md)

---

## 1. Learning Objectives

- Variable ka concept samajhna — ye kya hota hai aur kyun zaroori hai.
- Variable declare, initialize, aur reassign karna seekhna.

## 2. Prerequisites

Module 01 complete.

## 3. Concept in Simple Hinglish

Variable basically ek **container** ki tarah hota hai jisme hum value store kar sakte hain — jaise ek labeled box jisme tum koi bhi cheez rakh sakte ho aur baad mein us label se wapas nikaal sakte ho. JavaScript mein `let`, `const`, aur `var` variables declare karne ke different ways hain (detail next chapter mein).

## 4. Technical Explanation

Variable declaration mein 3 concepts hote hain:

1. **Declaration:** Variable ka naam engine ko batana (`let age;`)
2. **Initialization:** Variable ko pehli value dena (`age = 25;`)
3. **Assignment/Reassignment:** Variable ki value badalna (`age = 26;`)

```js
let age;        // Declaration (value: undefined)
age = 25;        // Initialization
age = 26;        // Reassignment
```

Ya sab ek line mein:
```js
let age = 25;   // Declaration + Initialization together
```

## 5. Syntax

```js
let variableName = value;
const variableName = value;
var variableName = value;
```

## 6. Basic Examples

```js
let userName = "Priya";
let userAge = 22;
let isStudent = true;

console.log(userName, userAge, isStudent);
```

## 7. Detailed Examples

```js
let score = 0; // Initial value

function increaseScore() {
  score = score + 10; // Reassignment
  console.log("Current score:", score);
}

increaseScore(); // Current score: 10
increaseScore(); // Current score: 20
increaseScore(); // Current score: 30
```

Ye example dikhata hai ki variable ki value **time ke saath change ho sakti hai** — yahi variables ka core purpose hai: state ko track karna.

## 8. Mental Model

Variable ko socho ek **labeled drawer/box** ki tarah. Box ka naam (`score`) fix rehta hai, lekin uske andar ki cheez (value) change hoti rehti hai. Jab tum `score` likhte ho code mein, JavaScript us box ko kholke andar ki current value nikaal ke deta hai.

## 9. What Happens Internally?

Jab tum `let score = 0;` likhte ho:
1. Engine memory mein ek slot allocate karta hai.
2. `score` naam ko us memory slot se **bind** kar deta hai (environment record mein entry banti hai).
3. Value `0` us slot mein store hoti hai.
4. Jab tum `score` reference karte ho, engine us binding ke through value fetch karta hai.

Ye detail Module 08 (Scope and Closures) mein "Execution Context" ke saath deeply cover hogi.

## 10. Common Mistakes

- Variable use karne se pehle declare na karna.
- Same variable ko different purposes ke liye reuse karna (confusing code) — better naya variable banao.
- Meaningless names use karna (`let x = getUserData();` — `x` se pata nahi chalta kya store hai; `userData` better hai).

## 11. Edge Cases

- Ek declared but uninitialized variable ki value `undefined` hoti hai, `null` nahi (dono alag hain — [04-Primitive-Types.md](./04-Primitive-Types.md) mein detail).
- Variable names case-sensitive hain — `Score` aur `score` do alag variables honge.

## 12. Real-World Usage

Variables har single JavaScript program ka foundation hain — user input store karna, calculations ka result rakhna, application state track karna — sab variables ke through hota hai.

## 13. Comparison With Related Concepts

| Term | Meaning |
|------|---------|
| Declaration | Variable ka naam register karna |
| Initialization | Pehli value dena |
| Assignment | Value set/change karna |

## 14. Practice Questions

1. `let city;` — is variable ki current value kya hogi?
2. Ek variable banao `price` naam ka, usme 100 store karo, phir usse 150 mein update karo.
3. Declaration aur initialization mein kya difference hai?

## 15. Challenge

Ek variable `balance` banao value 1000 ke saath. Phir isse 3 baar update karo — pehle 200 add karke, phir 50 subtract karke, phir final value console.log karo. Har step pe value print karo.

## 16. Interview Questions

**Q1: JavaScript mein variable declaration aur initialization mein kya farak hai?**
A: Declaration matlab variable ka naam engine ko register karna (`let x;`), jiski default value `undefined` hoti hai. Initialization matlab us variable ko pehli actual value dena (`x = 5;`). Dono ek saath bhi ho sakte hain: `let x = 5;`.

**Q2: Ek uninitialized variable ki value kya hoti hai?**
A: `undefined`. Jab `let`/`var` se declare kiya jaata hai bina value diye, JavaScript automatically `undefined` assign kar deta hai (const ke saath ye possible nahi kyunki const ko declare karte hi initialize karna mandatory hai).

## 17. Chapter Summary

Variable ek named container hai jisme values store hoti hain aur time ke saath change ho sakti hain. Declaration, initialization, aur assignment teen alag steps hain jo ek saath ya alag-alag ho sakte hain.

## 18. Revision Checklist

- [ ] Declaration, initialization, assignment ka difference clear hai.
- [ ] Uninitialized variable ki default value yaad hai.
- [ ] Meaningful variable naming ki importance samajh gayi.

---

**Next:** [02-var-let-const.md](./02-var-let-const.md)
