# Comments and Strict Mode

**Module:** 01-JavaScript-Foundations
**Difficulty:** 🟢 Beginner
**Previous:** [04-Syntax-and-Statements.md](./04-Syntax-and-Statements.md)
**Next:** [06-Errors-and-ASI.md](./06-Errors-and-ASI.md)

---

## 1. Learning Objectives

- Comments likhna aur unka correct use samajhna.
- Strict mode kya hai, kyun use karna chahiye, aur kaise enable karte hain.

## 2. Prerequisites

Modules 01-04.

## 3. Concept in Simple Hinglish

**Comments** wo lines hain jo JavaScript engine ignore karta hai — ye sirf humans (developers) ke liye hoti hain, code samajhne mein help karne ke liye. **Strict mode** JavaScript ka ek "stricter" version hai jo silent mistakes ko errors mein convert kar deta hai — isse bugs jaldi pakad mein aate hain.

## 4. Technical Explanation

**Two types of comments:**
```js
// Single-line comment

/* Multi-line
   comment */
```

**Strict mode** ek directive hai jo file ya function ke top pe `"use strict";` likh ke enable hoti hai. Ye JavaScript ke kuch "loose"/error-prone behaviors ko disable kar deta hai:
- Undeclared variables assign karna error dega (`x = 5;` bina `let/const/var` ke).
- Duplicate parameter names error denge.
- `this` global scope mein `undefined` hoga (non-strict mein `window`/`global` hota tha).
- Kuch reserved words future use ke liye protect ho jaate hain.

**Important:** ES6 modules aur classes **automatically strict mode** mein hote hain — tumhe explicitly likhne ki zaroorat nahi.

## 5. Syntax

```js
"use strict"; // File ke bilkul top pe

function myFunction() {
  "use strict"; // Ya sirf function ke top pe (function-level strict mode)
}
```

## 6. Basic Examples

```js
// Comments
// Yeh line ignore hogi
console.log("This runs"); // Ye inline comment bhi ignore hogi

/*
  Multiple lines
  ignore ho jaayengi
*/
```

## 7. Detailed Examples

```js
"use strict";

function demo() {
  x = 10; // ReferenceError: x is not defined
  console.log(x);
}

demo();
```

Bina strict mode ke, `x = 10;` (bina declaration) silently ek **global variable** create kar deta — ek common bug source. Strict mode isse turant error bana deta hai, taaki tumhe pata chale ki `let`/`const` bhool gaye.

```js
// Non-strict mode mein 'this' ka behavior
function showThis() {
  console.log(this);
}
showThis(); // Non-strict: global object (window/global) | Strict: undefined
```

## 8. Mental Model

Strict mode ko socho ek **strict teacher** ki tarah jo chhoti galtiyan bhi turant point out karti hai, taaki exam (production) mein bade mistakes na ho. Non-strict mode ek "lenient teacher" hai jo chhoti galtiyan ignore kar deta hai — lekin wahi galtiyan aage jaake bade bugs ban jaati hain.

## 9. What Happens Internally?

Engine parsing ke time `"use strict"` directive dekh kar us scope (file ya function) ke liye different set of rules apply karta hai internally — kuch operations jo normally silently fail/ignore hoti hain, unhe explicitly throw karne ka flag set ho jaata hai.

## 10. Common Mistakes

- Comments ko nested karna (`/* /* nested */ */`) — multi-line comments nest nahi hote, ye error dega.
- `"use strict"` ko file ke beech mein likhna — ye sirf top pe kaam karta hai (ya function ke bilkul start mein).
- Strict mode ka use na karna aur phir undeclared globals ke bugs debug karne mein time waste karna.

## 11. Edge Cases

- Agar ek file mein multiple functions hain aur sirf ek function ke top pe `"use strict"` hai, to sirf wahi function strict mode mein chalega, baaki file nahi.
- ES Modules (`import`/`export` wali files) **always strict** hoti hain, chahe tum `"use strict"` likho ya na likho.

## 12. Real-World Usage

Modern codebases (React, Node.js modules, TypeScript compiled output) almost always strict mode mein hoti hain — kyunki modules aur classes automatically strict hote hain. Legacy `<script>` tags mein explicit `"use strict"` dikhta hai.

## 13. Comparison With Related Concepts

| Aspect | Non-Strict Mode | Strict Mode |
|--------|------------------|--------------|
| Undeclared variable assignment | Silently creates global | ReferenceError |
| `this` in plain function call | Global object | `undefined` |
| Duplicate function parameters | Allowed | SyntaxError |

## 14. Practice Questions

1. Single-line aur multi-line comment ka syntax likho.
2. Strict mode mein undeclared variable assign karne pe kya hota hai?
3. Kya ES modules mein `"use strict"` likhna zaroori hai?

## 15. Challenge

Ek function likho jo strict mode mein ho, usme ek undeclared variable assign karne ki koshish karo, aur error message note karo. Phir same code non-strict mode mein try karo (differences observe karo).

## 16. Interview Questions

**Q1: Strict mode kyun use karna chahiye?**
A: Strict mode silent JavaScript mistakes (jaise undeclared globals, duplicate parameters) ko explicit errors mein convert kar deta hai, jisse bugs development ke time hi pakde jaate hain, production mein nahi. Ye code ko safer aur predictable banata hai.

**Q2: Kya classes aur ES modules ko explicitly strict mode declare karna padta hai?**
A: Nahi. ES6 classes aur ES modules (`import`/`export` syntax) automatically strict mode mein run hote hain — engine internally ye enforce karta hai, developer ko `"use strict"` likhne ki zaroorat nahi.

## 17. Chapter Summary

Comments code ko explain karne ke liye hote hain aur engine unhe ignore karta hai. Strict mode (`"use strict"`) JavaScript ko error-prone patterns pe strict banata hai — undeclared variables, duplicate parameters jaise mistakes ko silently allow karne ke bajaye errors throw karta hai. Classes aur modules automatically strict hote hain.

## 18. Revision Checklist

- [ ] Single-line aur multi-line comments likh sakta hoon.
- [ ] Strict mode enable karne ka syntax yaad hai.
- [ ] Strict mode ke 2-3 key differences yaad hain.

---

**Next:** [06-Errors-and-ASI.md](./06-Errors-and-ASI.md)
