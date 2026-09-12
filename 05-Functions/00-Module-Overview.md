# Module 05 — Functions

**Difficulty:** 🟡 Beginner-Intermediate
**Chapters:** 7
**Estimated time:** 8-12 hours
**Prerequisites:** [Module 04](../04-Control-Flow/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

**Ye course ka sabse important module hai.** Functions JavaScript ka core building block hain — aur is language mein functions sirf "code ka block" nahi hain, wo **values** hain jo variables mein store ho sakti hain, arguments ki tarah pass ho sakti hain, aur return ho sakti hain.

Yahin pe **Closures** aata hai — wo ek concept jo har JavaScript interview mein poocha jaata hai aur jiske bina async code, React hooks, ya koi bhi advanced pattern samajhna namumkin hai.

> ⏰ **Yahan jaldi mat karo.** Agar koi module dobara padhna pade, wo ye hai. Baaki poora course isi pe khada hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Function Basics](./01-Function-Basics.md) | Declaration vs expression, parameters, `return` |
| 02 | [Arrow Functions](./02-Arrow-Functions.md) | ✅ `=>` syntax aur `this` ka alag behavior |
| 03 | [Parameters, Defaults, Rest](./03-Parameters-Arguments-Defaults-Rest.md) | Default values, `...rest`, ⚠️ `arguments` object |
| 04 | [Callbacks & Higher-Order Functions](./04-Callbacks-and-Higher-Order-Functions.md) | Functions ko arguments ki tarah pass karna |
| 05 | [Recursion](./05-Recursion.md) | Function jo khud ko call kare — base case zaroori |
| 06 | [**Closures**](./06-Closures.md) | ⭐ Sabse important chapter — inner function outer variables yaad rakhta hai |
| 07 | [IIFE, Composition, Pure Functions](./07-IIFE-Composition-Pure-Functions.md) | Advanced patterns aur functional style |

---

## Is Module Ke Baad Tum

- [ ] Reusable functions likh paoge aur decide kar paoge kab arrow function use karna hai.
- [ ] **Closure ko example ke saath explain kar paoge** (counter banana aana chahiye, bina dekhe).
- [ ] Callbacks samajh jaoge — jo async JavaScript (Module 13) ki foundation hai.
- [ ] Recursion se nested problems solve kar paoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **Arrow functions ka apna `this` nahi hota** — wo surrounding scope se `this` uthate hain. Isliye object methods ke liye arrow function aksar galat choice hai. (Chapter 02, aur detail Module 08 mein)
- **Closure "magic" nahi hai** — inner function ko bas outer scope ka reference mil jaata hai, jo outer function return hone ke baad bhi memory mein zinda rehta hai. (Chapter 06)
- **`return` ke baad newline** — `return` aur value ko alag lines pe mat likho, ASI wahan semicolon daal dega aur `undefined` return hoga. (Module 01 ka ASI trap)
- **Recursion mein base case bhoolna** → stack overflow. (Chapter 05)

---

## Practice

- **Coding problems:** [28-Coding-Problems/02-Intermediate/](../28-Coding-Problems/02-Intermediate/01-Problems.md)
- **Projects:** [Calculator](../27-Projects/Beginner/01-Calculator/README.md), [Number Guessing Game](../27-Projects/Beginner/02-Number-Guessing-Game/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/05-Functions.md](../30-Revision/Cheat-Sheets/05-Functions.md)
- **Interview prep:** [29-Interview-Preparation/03-Functions-and-Closures.md](../29-Interview-Preparation/03-Functions-and-Closures.md)

---

**Shuru karo:** [01-Function-Basics.md](./01-Function-Basics.md)
**Agla Module:** [06-Arrays](../06-Arrays/00-Module-Overview.md)
