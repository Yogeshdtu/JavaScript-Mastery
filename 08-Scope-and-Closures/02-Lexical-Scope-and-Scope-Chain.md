# Lexical Scope and Scope Chain

**Module:** 08-Scope-and-Closures
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [01-Scope-Types.md](./01-Scope-Types.md)
**Next:** [03-Execution-Context-and-Call-Stack.md](./03-Execution-Context-and-Call-Stack.md)

---

## 1. Learning Objectives

- "Lexical scoping" ka exact matlab samajhna.
- Scope chain kaise banti hai aur variable lookup kaise hota hai, samajhna.
- Lexical scope vs dynamic scope ka conceptual difference jaanna.

## 2. Prerequisites

[01-Scope-Types.md](./01-Scope-Types.md)

## 3. Concept in Simple Hinglish

**Lexical scope** ka matlab hai — ek function/block ka scope **wahi decide hota hai jaha wo code mein LIKHA gaya hai**, na ki jaha se wo CALL hota hai. Ye JavaScript ka fundamental rule hai — "lexical" ka matlab hi hai "likhne ke time ka structure". **Scope chain** wo path hai jo engine follow karta hai variable dhundne ke liye — current scope se lekar sabse bahar (global) tak.

## 4. Technical Explanation

Jab function **define** hota hai, uska scope **permanently fix** ho jaata hai based on uski position code mein — chahe function kahi se bhi call ho, uski "outer scope reference" wahi rehti hai jo definition-time pe thi.

**Scope Chain:** Jab variable access karte ho, engine:
1. Current scope mein dhundta hai.
2. Nahi milne pe, immediate outer (parent) scope mein dhundta hai.
3. Ye process repeat hota hai jab tak global scope tak na pahunch jaaye.
4. Global scope mein bhi nahi mila, to `ReferenceError`.

## 5. Syntax

```js
let a = "global";

function outer() {
  let b = "outer";
  function inner() {
    let c = "inner";
    console.log(a, b, c); // Scope chain: inner → outer → global
  }
  inner();
}
outer();
```

## 6. Basic Examples

```js
function outer() {
  let message = "Hello from outer";

  function inner() {
    console.log(message); // Found via scope chain
  }

  inner();
}
outer(); // "Hello from outer"
```

## 7. Detailed Examples

**Lexical scope is fixed at definition time, not call time — proof:**
```js
let value = "global value";

function showValue() {
  console.log(value); // Which 'value' will this use?
}

function wrapper() {
  let value = "wrapper's local value";
  showValue(); // Calling showValue FROM here doesn't change its scope!
}

wrapper(); // "global value" — NOT "wrapper's local value"
```
`showValue` **jaha define hua** (global scope mein), usi ki scope chain follow karta hai — chahe usse `wrapper()` ke andar se call kiya ho. Iska scope wahi rehta hai jo definition ke time fix hua tha. Ye "lexical scoping" ka core proof hai — JavaScript **dynamic scoping** use nahi karta (jaha call-location scope decide karti).

**Scope chain lookup — step by step trace:**
```js
let level0 = "L0";

function levelA() {
  let level1 = "L1";

  function levelB() {
    let level2 = "L2";

    function levelC() {
      console.log(level2); // Step 1: Found in levelC's own scope? No.
                            // Step 2: Check levelB's scope — Found! "L2"
      console.log(level0); // Step 1-3: Not in levelC, levelB, levelA scopes.
                            // Step 4: Found in global scope — "L0"
    }
    levelC();
  }
  levelB();
}
levelA();
```

**Shadowing — inner variable "hides" outer variable of the same name:**
```js
let name = "Global Name";

function greet() {
  let name = "Local Name"; // This "shadows" the outer 'name'
  console.log(name); // "Local Name" — inner scope's variable wins
}

greet();
console.log(name); // "Global Name" — outer unaffected
```

## 8. Mental Model

Lexical scope ko socho ek **family tree ka address system** — tumhara "outer scope" tumhari **birth certificate** pe likha hai (jaha define hue), na ki tum abhi kaha reh rahe ho (jaha call hue). Scope chain ko socho ek **ladder** — variable dhundne ke liye tum apni current rung (scope) se upar chadhte jaate ho jab tak variable mile ya top (global) tak pahunch jao.

## 9. What Happens Internally?

Har function definition ke time, engine us function ke saath ek **internal reference** attach kar deta hai uske "enclosing lexical environment" ka — ye reference function ke lifetime tak preserve rehta hai (yahi closures ka foundation hai — Module 05, chapter 06). Variable lookup is chain of references ko traverse karta hai runtime pe.

## 10. Common Mistakes

- Sochna ki function call kaha se hui, wo uski scope decide karti hai (ye "dynamic scoping" hoti, JavaScript "lexical scoping" use karta hai).
- Variable shadowing ko accidental bug samajhna jab wo actually predictable lexical-scope behavior hai.
- Scope chain lookup ko performance-heavy samajhna — practically ye bahut fast hota hai, engines optimize karte hain.

## 11. Edge Cases

```js
function outer() {
  let x = 10;
  return function inner() {
    console.log(x);
  };
}

const fn1 = outer();
let x = 999; // Global 'x' — completely unrelated to outer's 'x'
fn1(); // 10 — inner still uses ITS lexical scope's x, not the global one
```

## 12. Real-World Usage

Lexical scoping closures ko possible banata hai, jo React Hooks, module patterns, aur event-handler state management ka foundation hai. Debugging karte waqt scope chain samajhna helps trace karne mein ki koi variable "kaha se aa raha hai".

## 13. Comparison With Related Concepts

| Concept | JavaScript Uses This? |
|---------|---------------------------|
| Lexical (Static) Scoping | ✅ Yes — scope fixed at write-time |
| Dynamic Scoping | ❌ No — some other languages (like Bash) use this |
| Scope Chain | ✅ The lookup mechanism for lexical scoping |

## 14. Practice Questions

1. Lexical scope "definition time" pe decide hota hai ya "call time" pe?
2. Scope chain lookup ka order kya hota hai (kaha se shuru, kaha end)?
3. Variable shadowing kya hai?

## 15. Challenge

Upar wala "lexical scope is fixed at definition time" example khud likho different variable names ke saath, aur predict karo output before running.

## 16. Interview Questions

**Q1: Lexical scope kya hai?**
A: Lexical scope ka matlab hai ki ek function ka scope uske **source code mein position** (jaha wo likha/defined gaya) se decide hota hai, na ki wo kaha se call hota hai. JavaScript "lexically scoped" language hai — function definition ke time hi uska outer-scope reference fix ho jaata hai, aur ye kabhi runtime call-location se change nahi hota. Yahi closures ka foundation hai.

**Q2: Scope chain kya hai aur variable lookup kaise hota hai?**
A: Scope chain wo sequence hai jise JavaScript engine follow karta hai kisi variable ko dhundne ke liye — pehle current (innermost) scope check hota hai, phir immediate outer scope, ye process repeat hota hai jab tak variable mil jaaye ya global scope tak pahunch jaaye. Agar global scope mein bhi variable nahi milta, `ReferenceError` throw hota hai. Ye chain function ke lexical (definition-time) nesting se banti hai.

## 17. Chapter Summary

Lexical scoping ka matlab hai function ka scope uski definition-location se fix hota hai, call-location se nahi. Scope chain variable-lookup ka mechanism hai — current scope se outward, global scope tak search karta hai. Shadowing tab hota hai jab inner scope ka variable same-name outer variable ko "hide" kar deta hai.

## 18. Revision Checklist

- [ ] "Definition-time vs call-time" scope difference clear hai.
- [ ] Scope chain lookup process trace kar sakta hoon.
- [ ] Variable shadowing ka concept samajh gaya.

---

**Next:** [03-Execution-Context-and-Call-Stack.md](./03-Execution-Context-and-Call-Stack.md)
