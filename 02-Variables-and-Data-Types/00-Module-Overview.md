# Module 02 — Variables and Data Types

**Difficulty:** 🟢 Beginner
**Chapters:** 8
**Estimated time:** 6-8 hours
**Prerequisites:** [Module 01](../01-JavaScript-Foundations/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Har program data store karta hai — naam, price, list, date. Ye module sikhata hai ki JavaScript mein **data rakha kaise jaata hai** (variables) aur **kis-kis type ka data hota hai** (string, number, object, etc.).

Yahan do cheezein extra dhyaan se padhna: **`var` vs `let` vs `const`** (har interview mein aata hai) aur **primitive vs reference types** (ye samajhe bina aage bahut confusing bugs aayenge).

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Variables Basics](./01-Variables-Basics.md) | Variable kya hai, naming rules, assignment |
| 02 | [var, let, const](./02-var-let-const.md) | ⚠️ `var` vs ✅ `let`/`const` — scope aur behavior |
| 03 | [Hoisting and TDZ](./03-Hoisting-and-TDZ.md) | Declarations "upar" kaise chale jaate hain, TDZ error |
| 04 | [Primitive Types](./04-Primitive-Types.md) | string, number, boolean, null, undefined, symbol, bigint |
| 05 | [Reference Types](./05-Reference-Types.md) | Objects/arrays memory mein kaise store hote hain |
| 06 | [Type Checking](./06-Type-Checking.md) | `typeof`, `instanceof`, `Array.isArray()` |
| 07 | [Regular Expressions](./07-Regular-Expressions.md) | Pattern matching — validation, search, replace |
| 08 | [Date and Intl](./08-Date-and-Intl.md) | Dates handle karna, locale-aware formatting |

---

## Is Module Ke Baad Tum

- [ ] `let` aur `const` confidently use karoge (aur `var` kyun avoid karna hai, wo bata paoge).
- [ ] Samajh jaoge ki `const` object ki properties kyun change ho jaati hain.
- [ ] Kisi bhi value ka type reliably check kar paoge.
- [ ] Email/phone validate karne wala regex likh paoge.
- [ ] Dates aur currency sahi format mein dikha paoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`const` ka matlab "value change nahi hogi" nahi hai** — matlab hai "variable ko dobara assign nahi kar sakte". `const arr = [1,2]` ke baad `arr.push(3)` bilkul chalega. (Chapter 02 + 05)
- **`arr2 = arr1` copy nahi banata** — dono same array ko point karte hain. Ek badloge, dono badlenge. (Chapter 05)
- **`typeof null` returns `"object"`** — ye JavaScript ka 1995 se chala aa raha bug hai, feature nahi. (Chapter 06)
- **Date ka month 0 se shuru hota hai** — `new Date(2024, 0, 15)` = January, February nahi. (Chapter 08)

---

## Practice

- **Coding problems:** [28-Coding-Problems/01-Beginner/](../28-Coding-Problems/01-Beginner/01-Problems.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/01-Variables-and-Data-Types.md](../30-Revision/Cheat-Sheets/01-Variables-and-Data-Types.md)

---

**Shuru karo:** [01-Variables-Basics.md](./01-Variables-Basics.md)
**Agla Module:** [03-Operators](../03-Operators/00-Module-Overview.md)
