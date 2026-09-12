# Module 16 — Fetch and APIs

**Difficulty:** 🟡 Intermediate
**Chapters:** 4
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 14 (async/await)](../14-Promises-and-Async-Await/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Ab tumhara code **internet se baat karega**. Ye module sikhata hai ki server se data kaise mangte hain, kaise bhejte hain, aur kaise real-world APIs ke saath kaam karte hain.

Yahin se tumhare projects "real" lagne lagte hain — Weather App, Movie Search, ya koi bhi app jo actual live data dikhaye.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [HTTP Basics](./01-HTTP-Basics.md) | Request/response, methods, status codes, headers |
| 02 | [Fetch API](./02-Fetch-API.md) | ✅ `fetch()`, JSON, POST requests, error handling |
| 03 | [REST and CRUD](./03-REST-and-CRUD.md) | REST design, CRUD → HTTP method mapping |
| 04 | [Auth, CORS, Rate Limiting, Pagination](./04-Auth-CORS-RateLimiting-Pagination.md) | Real-world API concerns |

---

## Is Module Ke Baad Tum

- [ ] Kisi bhi public API se data fetch karke page pe dikha paoge.
- [ ] POST/PUT/DELETE requests bhej paoge.
- [ ] Errors aur loading states properly handle kar paoge.
- [ ] CORS error dekh ke samajh jaoge ki wo **server-side** problem hai.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **⭐ `fetch()` 404 ya 500 pe reject NAHI hota!** Wo sirf network failure pe reject hota hai. 404 bhi ek "successful response" hai fetch ke liye. Isliye **hamesha** check karo:
  ```js
  const res = await fetch(url);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  const data = await res.json();
  ```
  Ye is module ka sabse important point hai, aur interviews mein aata hai. (Chapter 02)
- **CORS error frontend se fix nahi hota** — wo browser ki security hai, fix **server** pe headers se hota hai. Apne code mein kuch badalne se nahi jaayega. (Chapter 04)
- **`.json()` bhi async hai** — usko bhi `await` chahiye. (Chapter 02)
- **API keys frontend code mein mat daalo** — wo publicly visible hoti hain. (Chapter 04 + Module 23)

---

## Practice

- **Projects:** [Weather App](../27-Projects/Intermediate/01-Weather-App/README.md), [Movie Search App](../27-Projects/Intermediate/04-Movie-Search-Application/README.md), [Dashboard](../27-Projects/Intermediate/06-Dashboard/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/12-Fetch-and-Modules.md](../30-Revision/Cheat-Sheets/12-Fetch-and-Modules.md)

---

**Shuru karo:** [01-HTTP-Basics.md](./01-HTTP-Basics.md)
**Agla Module:** [17-Modules](../17-Modules/00-Module-Overview.md)
