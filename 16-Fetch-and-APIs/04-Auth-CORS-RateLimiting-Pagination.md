# Authentication, CORS, Rate Limiting, Pagination

**Module:** 16-Fetch-and-APIs
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [03-REST-and-CRUD.md](./03-REST-and-CRUD.md)
**Next:** [17-Modules/01-ES-Modules.md](../17-Modules/01-ES-Modules.md)

---

## 1. Learning Objectives

- Authentication (tokens) aur authorization ka concept differentiate karna.
- CORS kya hai aur ye kyun exist karta hai, samajhna.
- Rate limiting aur pagination ke API-design patterns samajhna.

## 2. Prerequisites

Module 16, chapters 01-03.

## 3. Concept in Simple Hinglish

Ye chapter 4 important real-world API concepts cover karta hai: **Authentication** (tum kaun ho?), **Authorization** (tumhe kya karne ki permission hai?), **CORS** (browser security jo cross-website requests ko control karti hai), aur **Rate Limiting/Pagination** (large-scale API management ke tools).

## 4. Technical Explanation

**Authentication vs Authorization:**
- **Authentication:** "Tum kaun ho?" verify karna (login, tokens).
- **Authorization:** "Tumhe ye action karne ki permission hai?" check karna (roles, permissions).

**CORS (Cross-Origin Resource Sharing):** Browser security mechanism jo default se **different origin** (domain/port/protocol) se requests ko block karta hai, jab tak server explicitly allow na kare (specific headers ke through).

**Rate Limiting:** Server ek client ko ek time-window mein sirf **limited number of requests** allow karta hai (abuse prevent karne ke liye).

**Pagination:** Large datasets ko **chhote pages mein todhna** taaki ek saath sab data fetch na karna pade.

## 5. Syntax

```js
// Authentication header
fetch(url, { headers: { "Authorization": "Bearer <token>" } });

// Pagination query params
fetch(`${url}?page=2&limit=20`);
```

## 6. Basic Examples

```js
const token = localStorage.getItem("authToken");

fetch("https://api.example.com/profile", {
  headers: { "Authorization": `Bearer ${token}` }
})
  .then(res => res.json())
  .then(data => console.log(data));
```

## 7. Detailed Examples

**Authentication flow — token-based (JWT-style) pattern:**
```js
async function login(email, password) {
  const response = await fetch("https://api.example.com/login", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ email, password })
  });
  const data = await response.json();
  localStorage.setItem("authToken", data.token); // Store the token for future requests
  return data;
}

async function fetchProtectedResource() {
  const token = localStorage.getItem("authToken");
  const response = await fetch("https://api.example.com/protected", {
    headers: { "Authorization": `Bearer ${token}` }
  });
  if (response.status === 401) {
    console.log("Token expired or invalid — please log in again");
    // Redirect to login
  }
  return response.json();
}
```
**⚠️ Security note (detailed in Module 23):** Storing tokens in `localStorage` is vulnerable to XSS attacks. `httpOnly` cookies are a more secure alternative for sensitive tokens.

**CORS — understanding the error and the fix:**
```js
// If your frontend (running on http://localhost:3000) tries to fetch from
// https://api.differentdomain.com WITHOUT proper CORS headers, you'll see:
// "Access to fetch at '...' from origin 'http://localhost:3000' has been blocked by CORS policy"

// This is NOT something you fix in your frontend JavaScript — the SERVER must send headers like:
// Access-Control-Allow-Origin: http://localhost:3000
// (or Access-Control-Allow-Origin: * for public APIs)
```
**Preflight requests:** Complex requests (jaise custom headers ya non-GET/POST methods) browser pehle ek automatic `OPTIONS` request bhejta hai ("preflight") server se check karne ke liye ki actual request allowed hai ya nahi — ye JavaScript code likhe bina automatically hota hai.

**Rate limiting — handling 429 responses:**
```js
async function fetchWithRateLimitHandling(url) {
  const response = await fetch(url);

  if (response.status === 429) {
    const retryAfter = response.headers.get("Retry-After"); // Server tells you how long to wait
    console.log(`Rate limited! Retry after ${retryAfter} seconds`);
    return null;
  }

  return response.json();
}
```
Rate limiting responses usually headers ke through info dete hain: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `Retry-After`.

**Pagination — common patterns:**
```js
// Pattern 1: Page-based pagination
async function getUsersPage(page = 1, limit = 20) {
  const response = await fetch(`https://api.example.com/users?page=${page}&limit=${limit}`);
  return response.json();
  // Typical response: { data: [...], totalPages: 10, currentPage: 1, totalItems: 200 }
}

// Pattern 2: Cursor-based pagination (better for large, frequently-changing datasets)
async function getUsersCursor(cursor = null) {
  const url = cursor
    ? `https://api.example.com/users?cursor=${cursor}`
    : `https://api.example.com/users`;
  const response = await fetch(url);
  return response.json();
  // Typical response: { data: [...], nextCursor: "abc123" }
}

// Fetching ALL pages (use cautiously — can be slow/expensive for large datasets)
async function getAllUsers() {
  let allUsers = [];
  let page = 1;
  let hasMore = true;

  while (hasMore) {
    const result = await getUsersPage(page);
    allUsers = allUsers.concat(result.data);
    hasMore = page < result.totalPages;
    page++;
  }

  return allUsers;
}
```

## 8. Mental Model

Authentication/Authorization ko socho **office ka security check** — Authentication ID card check karna hai ("tum John ho?"), Authorization decide karta hai kaunse floors/rooms tum access kar sakte ho ("John ko sirf floor 3 tak allowed hai"). CORS ko socho ek **building ki policy jo decide karti hai kaunse "outside" visitors (websites) andar aa sakte hain**. Rate limiting ek **"ek din mein sirf 5 baar cafeteria visit kar sakte ho"** wala rule hai. Pagination ek **kitab ko chapters mein todhna** hai poori kitab ek saath dene ke bajaye.

## 9. What Happens Internally?

CORS browser-enforced hai — server response headers bhejta hai, browser inhe check karke decide karta hai response ko JavaScript ko dena hai ya block karna hai. Rate limiting server-side counter maintain karta hai per-client (IP/token-based) requests count karke. Pagination database-level pe `LIMIT`/`OFFSET` (SQL) ya similar mechanisms use karta hai (Module 20 mein detail).

## 10. Common Mistakes

- Sensitive tokens ko `localStorage` mein store karna bina XSS-risk samjhe (Module 23 mein detail).
- CORS errors ko frontend code se "fix" karne ki koshish karna — ye hamesha server-side configuration issue hai.
- Rate-limited responses ko silently fail hone dena bina retry-logic ke.
- "Fetch all pages in a loop" pattern ko bina consideration ke use karna bahut large datasets ke liye — performance/cost issue ho sakta hai.

## 11. Edge Cases

Kuch APIs "sliding window" rate limiting use karte hain jabki kuch "fixed window" — behavior slightly different ho sakta hai edge cases mein (jaise window boundary ke paas burst requests).

## 12. Real-World Usage

Almost har production API authentication (login systems), authorization (role-based access), CORS configuration (agar frontend/backend different domains pe hain), rate limiting (abuse prevention), aur pagination (large datasets) implement karti hai.

## 13. Comparison With Related Concepts

| Concept | Solves |
|---------|--------|
| Authentication | "Who are you?" |
| Authorization | "What can you do?" |
| CORS | Browser security for cross-origin requests |
| Rate Limiting | Preventing API abuse |
| Pagination | Efficiently handling large datasets |

## 14. Practice Questions

1. Authentication aur Authorization mein kya difference hai?
2. CORS error ko frontend code se fix kiya ja sakta hai?
3. Page-based aur cursor-based pagination mein kya difference hai?

## 15. Challenge

Ek function `fetchAllPages(baseUrl)` likho jo page-based pagination follow kare aur saare pages ka data ek single array mein combine kare, `hasMore`/`totalPages` logic use karke (fictional API assume karke, jaisa upar example mein tha).

## 16. Interview Questions

**Q1: CORS kya hai aur ye browsers mein kyun exist karta hai?**
A: CORS (Cross-Origin Resource Sharing) ek browser security mechanism hai jo by default ek website (origin) ko **doosre origin** (different domain/port/protocol) se data fetch karne se **rokta hai** — ye "Same-Origin Policy" ko relax karne ka controlled tareeka hai. Ye exist karta hai malicious websites ko prevent karne ke liye ki wo silently user ke browser se dusri websites (jaise banking sites, jaha user already logged in ho) se sensitive data na fetch kar sakein. Server explicitly `Access-Control-Allow-Origin` header bhej ke bata sakta hai kaunse origins allowed hain — ye hamesha **server-side** configuration hai, frontend code se "fix" nahi ki ja sakti.

**Q2: Page-based aur cursor-based pagination mein kya difference hai?**
A: **Page-based pagination** page number aur limit use karta hai (`?page=2&limit=20`) — simple hai, but agar dataset frequently change ho raha ho (items add/delete ho rahe hon), "page shifting" issues aa sakte hain (jaise ek item skip ho jaana ya duplicate dikhna). **Cursor-based pagination** ek "pointer" (cursor, usually last-seen-item ka ID/token) use karta hai next batch fetch karne ke liye — ye zyada stable hai frequently-changing, large datasets ke liye (jaise social media feeds), kyunki ye absolute position pe depend nahi karta.

## 17. Chapter Summary

Authentication verify karta hai "kaun ho", Authorization decide karta hai "kya kar sakte ho". CORS browser-security hai jo cross-origin requests control karti hai — server-side fix chahiye. Rate limiting API abuse prevent karta hai (429 responses). Pagination large datasets ko manageable chunks mein deliver karta hai — page-based ya cursor-based approaches ke saath.

## 18. Revision Checklist

- [ ] Authentication vs Authorization ka difference clear hai.
- [ ] CORS ka concept aur "server-side fix" requirement yaad hai.
- [ ] Rate limiting aur pagination ke basic patterns samajh gaye.

---

**Module 16 Complete!** Next Module: [17-Modules/01-ES-Modules.md](../17-Modules/01-ES-Modules.md)
