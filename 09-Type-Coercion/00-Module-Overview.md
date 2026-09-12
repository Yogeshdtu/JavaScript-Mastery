# Module 09 — Type Coercion

**Difficulty:** 🟠 Intermediate
**Chapters:** 4
**Estimated time:** 4-6 hours
**Prerequisites:** [Module 02 (Data Types)](../02-Variables-and-Data-Types/00-Module-Overview.md), [Module 03 (Operators)](../03-Operators/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

JavaScript ki sabse "weird" cheezein — `[] == false` true kyun hai, `"5" + 3` `"53"` kyun deta hai, `NaN === NaN` false kyun hai — sab **type coercion** ki wajah se hain.

Coercion matlab: JavaScript automatically ek type ko dusre type mein **chupke se convert** kar deti hai. Ye module us "chupke se" ko poori tarah explicit bana deta hai, taaki tumhe kabhi surprise na ho.

> Ye module padhne ke baad JavaScript ke wo saare "WTF" memes tumhe actually **logical** lagenge.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Truthy and Falsy](./01-Truthy-and-Falsy.md) | 8 falsy values — baaki sab truthy |
| 02 | [Implicit vs Explicit Coercion](./02-Implicit-vs-Explicit-Coercion.md) | Automatic conversion vs `Number()`/`String()` |
| 03 | [Equality Deep Dive](./03-Equality-Deep-Dive.md) | ⭐ `==` ka poora algorithm, step by step |
| 04 | [Object-to-Primitive Conversion](./04-Object-to-Primitive-Conversion.md) | `valueOf`, `toString`, `Symbol.toPrimitive` |

---

## Is Module Ke Baad Tum

- [ ] 8 falsy values memory se bata paoge.
- [ ] `[] == false` ko **step-by-step derive** kar paoge (classic interview question).
- [ ] Samajh jaoge `+` dusre math operators se alag kyun behave karta hai.
- [ ] Explicit conversion (`Number()`, `String()`, `Boolean()`) prefer karoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`[]` truthy hai, lekin `[] == false` bhi true hai!** Ye contradiction nahi hai — `if([])` Boolean conversion karta hai, `==` Abstract Equality Algorithm follow karta hai. Do alag rules. (Chapter 01 + 03)
- **`+` ka double role** — agar koi bhi side string hai, wo concatenation karta hai. Baaki operators (`-`, `*`, `/`) hamesha numbers force karte hain. Isliye `"5" + 3 = "53"` lekin `"5" - 3 = 2`. (Chapter 02)
- **`NaN === NaN` is `false`** — JavaScript ki ekmatra value jo khud ke barabar nahi. Check karne ke liye `Number.isNaN()` use karo. (Chapter 03)
- **`==` transitive nahi hai** — isliye industry standard hai: hamesha `===`. (Chapter 03)

---

## Practice

- **Coding problems:** [28-Coding-Problems/05-Output-Prediction/](../28-Coding-Problems/05-Output-Prediction/01-Problems.md) — ye module us folder ke liye hi bana hai.
- **Gotchas:** [30-Revision/JavaScript-Gotchas.md](../30-Revision/JavaScript-Gotchas.md) — sections 1, 2, 6, 16, 17.
- **Interview prep:** [29-Interview-Preparation/02-Core-JavaScript.md](../29-Interview-Preparation/02-Core-JavaScript.md)

---

**Shuru karo:** [01-Truthy-and-Falsy.md](./01-Truthy-and-Falsy.md)
**Agla Module:** [10-Modern-JavaScript](../10-Modern-JavaScript/00-Module-Overview.md)
