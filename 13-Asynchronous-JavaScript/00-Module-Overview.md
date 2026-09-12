# Module 13 — Asynchronous JavaScript

**Difficulty:** 🟠 Advanced
**Chapters:** 4
**Estimated time:** 6-9 hours
**Prerequisites:** [Module 05 (Functions)](../05-Functions/00-Module-Overview.md) — callbacks aur closures solid hone chahiye

---

## Is Module Mein Kya Hai?

Abhi tak tumhara saara code **turant** chalta tha, line by line. Lekin real apps mein bahut kaam **time leta hai** — server se data mangana, file padhna, 2 second ka timer. Agar JavaScript in sab ka wait karti, to poora page freeze ho jaata.

Ye module (aur agle do) samjhate hain ki JavaScript **single thread hone ke bawajood** ye sab kaise handle karti hai.

> ⚠️ Ye course ka **sabse conceptually hard** hissa hai (Modules 13-15). Agar confusing lage — ye normal hai. Slow chalo, examples khud run karo, aur Module 15 (Event Loop) tak zaroor pahuncho — wahan sab jud jaata hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Sync vs Async](./01-Sync-vs-Async.md) | Blocking vs non-blocking, JS single-threaded kyun hai |
| 02 | [Callbacks and Callback Hell](./02-Callbacks-and-Callback-Hell.md) | ⚠️ Purana tareeka aur uski problem |
| 03 | [Promises Deep Dive](./03-Promises-Deep-Dive.md) | ✅ States, `.then`/`.catch`/`.finally`, chaining |
| 04 | [Promise Combinators](./04-Promise-Combinators.md) | `all`, `allSettled`, `race`, `any` |

---

## Is Module Ke Baad Tum

- [ ] Samajh jaoge ki `setTimeout(fn, 0)` turant kyun nahi chalta.
- [ ] Promise chains likh paoge proper error handling ke saath.
- [ ] Callback hell ko Promise chain mein convert kar paoge.
- [ ] `Promise.all` se multiple requests **parallel** chala paoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **"Single-threaded hai to async kaise?"** — JavaScript khud ek thread hai, lekin timers/network browser (ya Node) handle karta hai, JS engine nahi. Kaam done hone pe callback queue mein aata hai. (Chapter 01)
- **Promise banate hi chal padta hai** — `new Promise(...)` ka executor **turant** chalta hai, `.then()` ka wait nahi karta. (Chapter 03)
- **`.then()` mein `return` karna zaroori hai** — warna agli `.then()` ko `undefined` milega. Chaining ka poora point hi return karna hai. (Chapter 03)
- **`Promise.all` fail-fast hai** — ek bhi reject hua to poora reject. Sabka result chahiye chahe koi fail ho, to `allSettled` use karo. (Chapter 04)

---

## Practice

- **Projects:** [Weather App](../27-Projects/Intermediate/01-Weather-App/README.md), [Movie Search App](../27-Projects/Intermediate/04-Movie-Search-Application/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/09-Promises-and-Async-Await.md](../30-Revision/Cheat-Sheets/09-Promises-and-Async-Await.md)
- **Interview prep:** [29-Interview-Preparation/05-Async-JavaScript.md](../29-Interview-Preparation/05-Async-JavaScript.md)

---

**Shuru karo:** [01-Sync-vs-Async.md](./01-Sync-vs-Async.md)
**Agla Module:** [14-Promises-and-Async-Await](../14-Promises-and-Async-Await/00-Module-Overview.md)
