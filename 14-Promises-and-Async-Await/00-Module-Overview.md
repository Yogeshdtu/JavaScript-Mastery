# Module 14 — Promises and Async/Await

**Difficulty:** 🟠 Advanced
**Chapters:** 3
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 13](../13-Asynchronous-JavaScript/00-Module-Overview.md) — Promises samajhna zaroori hai

---

## Is Module Mein Kya Hai?

`async`/`await` modern JavaScript ka sabse **comfortable** feature hai — ye async code ko bilkul normal, sync code jaisa dikhata hai, jabki andar se wo Promises hi use karta hai.

Ye module tumhe sirf syntax nahi sikhayega, balki ye bhi ki `await` actually karta kya hai, error handling kaise badalti hai, aur **sabse important** — accidentally sequential code likh ke apni app slow kaise nahi karni.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Async/Await Basics](./01-Async-Await-Basics.md) | ✅ `async`, `await`, Promise se relation |
| 02 | [Error Handling in Async Code](./02-Error-Handling-in-Async-Code.md) | `try/catch`, unhandled rejections |
| 03 | [**Sequential vs Parallel**](./03-Sequential-vs-Parallel-Execution.md) | ⭐ Apne code ko 3x fast banane wala chapter |

---

## Is Module Ke Baad Tum

- [ ] Clean async code likh paoge `async`/`await` se.
- [ ] Async errors properly handle kar paoge.
- [ ] **Pehchan paoge kab tumhara `await` unnecessarily slow kar raha hai** aur `Promise.all` se fix kar paoge.
- [ ] Bata paoge ki async function hamesha Promise return karta hai.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **Ye sabse common real-world performance bug hai:**
  ```js
  // ❌ SLOW — 3 seconds (ek ke baad ek)
  const a = await fetchA();
  const b = await fetchB();
  const c = await fetchC();

  // ✅ FAST — 1 second (saath saath)
  const [a, b, c] = await Promise.all([fetchA(), fetchB(), fetchC()]);
  ```
  Agar requests ek dusre pe depend nahi karti, unhe sequential mat chalao. (Chapter 03)
- **`await` poori app ko nahi rokta** — sirf us ek async function ko pause karta hai. Baaki code chalta rehta hai. (Chapter 01)
- **`async function` hamesha Promise return karta hai** — `return 42` ka matlab `Promise<42>`, seedha `42` nahi. (Chapter 01)
- **`.forEach` ke andar `await` kaam nahi karta** — `for...of` loop ya `Promise.all` + `map` use karo. (Chapter 03)
- **`try/catch` ke bina rejected promise silently fail ho sakta hai.** (Chapter 02)

---

## Practice

- **Coding problems:** [28-Coding-Problems/03-Advanced/](../28-Coding-Problems/03-Advanced/01-Problems.md)
- **Projects:** [Weather App](../27-Projects/Intermediate/01-Weather-App/README.md), [Dashboard](../27-Projects/Intermediate/06-Dashboard/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/09-Promises-and-Async-Await.md](../30-Revision/Cheat-Sheets/09-Promises-and-Async-Await.md)

---

**Shuru karo:** [01-Async-Await-Basics.md](./01-Async-Await-Basics.md)
**Agla Module:** [15-Event-Loop](../15-Event-Loop/00-Module-Overview.md)
