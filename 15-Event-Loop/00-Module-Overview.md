# Module 15 — Event Loop

**Difficulty:** 🔴 Advanced
**Chapters:** 3
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 13](../13-Asynchronous-JavaScript/00-Module-Overview.md), [Module 14](../14-Promises-and-Async-Await/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Ye wo module hai jahan **Modules 13 aur 14 ka sab kuch jud jaata hai**. Event Loop wo mechanism hai jo decide karta hai ki kaunsa code **kab** chalega.

Iske baad tum kisi bhi mixed `setTimeout` + `Promise` + normal code ka output order **predict** kar paoge — aur yahi wo cheez hai jo interviews mein sabse zyada poochi jaati hai async section mein.

> 🎯 **Interview reality:** "Is code ka output order kya hoga?" — ye question almost har mid-level JavaScript interview mein aata hai. Ye module uska direct answer hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Call Stack, Heap, Web APIs](./01-Call-Stack-Heap-and-WebAPIs.md) | Runtime ke parts aur unka kaam |
| 02 | [Task Queue vs Microtask Queue](./02-Task-Queue-vs-Microtask-Queue.md) | ⭐ Microtasks hamesha pehle kyun chalte hain |
| 03 | [Step-By-Step Examples](./03-Event-Loop-Step-By-Step-Examples.md) | Real snippets trace karna, line by line |

---

## Is Module Ke Baad Tum

- [ ] **Kisi bhi async snippet ka exact output order** trace kar paoge.
- [ ] Bata paoge `setTimeout(fn, 0)` `Promise.then` ke baad kyun chalta hai.
- [ ] Samajh jaoge ki heavy loop poori page ko kyun freeze kar deta hai.
- [ ] Call stack, queues, aur event loop ka diagram bana paoge.

---

## The One Rule Jo Sab Samjha Deta Hai

```
1. Call Stack ka saara sync code chalao
2. Stack khaali? → POORI Microtask Queue drain karo (Promises)
3. Phir SIRF EK Macrotask lo (setTimeout, etc.)
4. Wapas step 2 pe jao
```

**Microtasks (Promises) hamesha Macrotasks (setTimeout) se pehle chalte hain** — chahe timeout `0ms` ka ho.

```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
// Output: 1, 4, 3, 2
```

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`setTimeout(fn, 1000)` guarantee nahi karta ki 1 second baad chalega** — wo guarantee karta hai "**kam se kam** 1 second". Agar stack busy hai, aur der lagegi. (Chapter 01)
- **Microtask queue poori drain hoti hai, ek-ek nahi** — agar ek microtask aur microtasks banaye, wo bhi usi turn mein chalenge. (Chapter 02)
- **`async/await` bhi microtask hi hai** — `await` ke baad ka code microtask queue mein jaata hai. (Chapter 02)

---

## Practice

- **Coding problems:** [28-Coding-Problems/05-Output-Prediction/](../28-Coding-Problems/05-Output-Prediction/01-Problems.md) — ye module usi ke liye hai.
- **Cheat sheet:** [30-Revision/Cheat-Sheets/10-Event-Loop.md](../30-Revision/Cheat-Sheets/10-Event-Loop.md)
- **Interview prep:** [29-Interview-Preparation/08-Output-Based-Questions.md](../29-Interview-Preparation/08-Output-Based-Questions.md)

---

**Shuru karo:** [01-Call-Stack-Heap-and-WebAPIs.md](./01-Call-Stack-Heap-and-WebAPIs.md)
**Agla Module:** [16-Fetch-and-APIs](../16-Fetch-and-APIs/00-Module-Overview.md)
