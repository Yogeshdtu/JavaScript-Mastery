# Module 22 — Performance

**Difficulty:** 🔴 Advanced
**Chapters:** 4
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 06 (Arrays)](../06-Arrays/00-Module-Overview.md), [Module 15 (Event Loop)](../15-Event-Loop/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Code chal jaana kaafi nahi — use **fast** bhi chalna chahiye. Ye module sikhata hai ki slow code ko pehchano kaise, aur fix kaise karo.

Yahan ke do topics — **Big O** aur **debounce/throttle** — interviews mein bahut common hain, aur real apps mein turant visible difference laate hain.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Big O Basics](./01-Big-O-Basics.md) | Code ki speed measure karna, nested loops ka khatra |
| 02 | [Memory & Garbage Collection](./02-Memory-and-Garbage-Collection.md) | Memory leaks kaise hote hain aur kaise rokein |
| 03 | [**Debounce, Throttle, Lazy Loading**](./03-Debounce-Throttle-Lazy-Loading.md) | ⭐ Interview favourite + practical |
| 04 | [Caching & Network Optimization](./04-Caching-and-Network-Optimization.md) | Results cache karna, requests kam karna |

---

## Is Module Ke Baad Tum

- [ ] **Debounce aur throttle memory se likh paoge** (classic interview question).
- [ ] Accidental O(n²) loops pehchan paoge.
- [ ] Memory leaks ke 3 common causes bata paoge.
- [ ] Search inputs aur scroll handlers ko optimize kar paoge.

---

## Debounce vs Throttle — Ek Line Mein

| | Kab chalta hai | Kahan use karo |
|---|---|---|
| **Debounce** | User **ruk** jaaye uske baad | Search box (typing khatam hone pe API call) |
| **Throttle** | Fixed interval pe, maximum ek baar | Scroll/resize handlers |

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **Nested loop = O(n²)** — 1000 items pe 10 lakh operations. Array ke andar array search kar rahe ho? `Set` ya `Map` use karo, wo O(1) lookup deta hai. (Chapter 01)
- **Memory leaks ke 3 main causes:** event listeners jo kabhi remove nahi hue, timers (`setInterval`) jo clear nahi hue, aur accidental global variables. (Chapter 02)
- **Premature optimization time waste hai** — pehle **measure** karo (DevTools Performance tab), phir fix karo. Guess mat karo. (Chapter 01)
- **Debounce aur throttle ko ulta samajhna** — sabse common confusion. Upar wali table yaad rakho. (Chapter 03)

---

## Practice

- **Coding problems:** [28-Coding-Problems/06-Interview-Level/](../28-Coding-Problems/06-Interview-Level/01-Problems.md) — debounce/throttle implement karo.
- **Projects:** [Movie Search App](../27-Projects/Intermediate/04-Movie-Search-Application/README.md) — debounce ka perfect real use-case.

---

**Shuru karo:** [01-Big-O-Basics.md](./01-Big-O-Basics.md)
**Agla Module:** [23-Security](../23-Security/00-Module-Overview.md)
