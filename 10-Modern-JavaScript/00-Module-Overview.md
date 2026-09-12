# Module 10 — Modern JavaScript (ES6+)

**Difficulty:** 🟠 Intermediate-Advanced
**Chapters:** 7
**Estimated time:** 8-12 hours
**Prerequisites:** [Module 08 (Scope & Closures)](../08-Scope-and-Closures/00-Module-Overview.md) — especially `this`

---

## Is Module Mein Kya Hai?

2015 (ES6) ke baad JavaScript bahut badal gayi. Ye module wo saara "modern JavaScript" cover karta hai jo aaj ke real codebases mein actually likha jaata hai — classes, prototypes, Map/Set, generators, symbols.

Sabse important chapter hai **Prototypes Deep Dive**. Classes ek sundar syntax hai, lekin **JavaScript mein asli inheritance prototypes se hoti hai** — aur interviews mein yahi poocha jaata hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [ES6+ Overview](./01-ES6-Plus-Overview.md) | Kaunse features kab aaye, kya use karna hai |
| 02 | [Classes and Inheritance](./02-Classes-and-Inheritance.md) | ✅ `class`, `extends`, `super`, static members |
| 03 | [**Prototypes Deep Dive**](./03-Prototypes-Deep-Dive.md) | ⭐ Classes ke neeche actually kya hai |
| 04 | [Map, Set, WeakMap, WeakSet](./04-Map-Set-WeakMap-WeakSet.md) | Naye collection types aur kab use karein |
| 05 | [Iterators and Generators](./05-Iterators-and-Generators.md) | `function*`, `yield`, custom iteration |
| 06 | [Symbols](./06-Symbols.md) | Unique keys, well-known symbols |
| 07 | [Private Fields & Other Features](./07-Private-Fields-and-Other-Features.md) | `#private`, optional chaining, latest additions |

---

## Is Module Ke Baad Tum

- [ ] Classes likh paoge inheritance ke saath.
- [ ] **Bata paoge ki "classes sirf syntactic sugar hain"** aur prototype chain diagram bana paoge.
- [ ] Decide kar paoge kab plain object chahiye aur kab `Map`.
- [ ] Generators se custom iterables bana paoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **Classes real classes nahi hain** — ye prototypes ke upar ek syntax layer hai. Java/C++ wale log yahan sabse zyada confuse hote hain. (Chapter 02 + 03)
- **`super()` ko `this` se pehle call karna zaroori hai** child constructor mein — warna ReferenceError. (Chapter 02)
- **`Map` vs Object** — `Map` mein koi bhi cheez key ban sakti hai (objects bhi!), insertion order guaranteed hai, aur `.size` milta hai. Object keys hamesha strings/symbols hote hain. (Chapter 04)
- **`WeakMap` garbage collection allow karta hai** — isliye memory leaks avoid karne ke liye use hota hai. (Chapter 04)

---

## Practice

- **Coding problems:** [28-Coding-Problems/03-Advanced/](../28-Coding-Problems/03-Advanced/01-Problems.md)
- **Projects:** [Shopping Cart](../27-Projects/Intermediate/05-Shopping-Cart/README.md), [Dashboard](../27-Projects/Intermediate/06-Dashboard/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/08-Prototypes.md](../30-Revision/Cheat-Sheets/08-Prototypes.md)
- **Interview prep:** [29-Interview-Preparation/04-Objects-and-Prototypes.md](../29-Interview-Preparation/04-Objects-and-Prototypes.md)

---

**Shuru karo:** [01-ES6-Plus-Overview.md](./01-ES6-Plus-Overview.md)
**Agla Module:** [11-DOM-and-Browser](../11-DOM-and-Browser/00-Module-Overview.md)
