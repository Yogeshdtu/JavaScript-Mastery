# HTTP Servers and Routing (with Express)

**Module:** 19-Backend-JavaScript
**Difficulty:** 🟠 Advanced
**Previous:** [18-NodeJS/05-HTTP-and-CLI-Apps.md](../18-NodeJS/05-HTTP-and-CLI-Apps.md)
**Next:** [02-Middleware-and-REST-APIs.md](./02-Middleware-and-REST-APIs.md)

---

## 1. Learning Objectives

- Express.js (mainstream Node.js framework) se server banana.
- Clean routing syntax use karna (route parameters, query parameters).
- Raw `http` module (Module 18) se Express ka comparison samajhna.

## 2. Prerequisites

Module 18 complete, especially chapter 05.

## 3. Concept in Simple Hinglish

**Express.js** Node.js ka sabse popular web framework hai — ye `http` module ke upar ek convenient layer hai jo routing, middleware, aur request/response handling ko bahut simpler bana deta hai. Is course mein hum Express ko example framework ki tarah use karenge backend concepts explain karne ke liye.

## 4. Technical Explanation

Express mein routes `app.METHOD(path, handler)` pattern follow karte hain — `app.get()`, `app.post()`, `app.put()`, `app.delete()`, etc. **Route parameters** (`:id`) dynamic URL segments capture karte hain. **Query parameters** (`?key=value`) URL ke end mein optional data carry karte hain.

## 5. Syntax

```js
const express = require("express");
const app = express();

app.get("/path", (req, res) => {
  res.send("response");
});

app.listen(3000);
```

## 6. Basic Examples

```js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Welcome to the API!");
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

## 7. Detailed Examples

**Comparing raw `http` (Module 18) with Express — the improvement:**
```js
// ⚠️ Raw http module (Module 18, chapter 05) — verbose manual routing
// if (req.url === "/users" && req.method === "GET") { ... }

// ✅ Express — clean, declarative routing
const express = require("express");
const app = express();

app.get("/users", (req, res) => {
  res.json([{ id: 1, name: "Meher" }, { id: 2, name: "Yash" }]);
});

app.listen(3000);
```

**Route parameters — capturing dynamic URL segments:**
```js
app.get("/users/:id", (req, res) => {
  const userId = req.params.id; // Captures whatever is in the URL at that position
  res.json({ id: userId, name: `User ${userId}` });
});

// GET /users/42  →  req.params.id === "42"
```

**Multiple route parameters:**
```js
app.get("/users/:userId/orders/:orderId", (req, res) => {
  const { userId, orderId } = req.params; // Destructuring (Module 07, chapter 02)!
  res.json({ userId, orderId });
});

// GET /users/5/orders/12  →  { userId: "5", orderId: "12" }
```

**Query parameters — for optional/filtering data:**
```js
app.get("/products", (req, res) => {
  const { category, sort, limit } = req.query;
  console.log(category, sort, limit);
  // GET /products?category=shoes&sort=price&limit=10
  // → category="shoes", sort="price", limit="10"

  res.json({ message: `Fetching ${category} products, sorted by ${sort}` });
});
```

**Setting up JSON body parsing (built into modern Express):**
```js
app.use(express.json()); // Middleware (full detail next chapter) — parses JSON request bodies

app.post("/users", (req, res) => {
  const newUser = req.body; // No manual stream-collecting needed, unlike raw http (Module 18)!
  console.log("Received:", newUser);
  res.status(201).json({ message: "User created", user: newUser });
});
```

**Handling 404s — a catch-all route:**
```js
app.use((req, res) => {
  res.status(404).json({ error: "Route not found" });
});
```

## 8. Mental Model

Express ko socho ek **traffic control system** for tumhari application — har incoming request (car) ek specific route (road) follow karti hai, aur Express decide karta hai kaunsa handler (destination) us request ko serve karega, based on URL aur HTTP method (jaise road-signs).

## 9. What Happens Internally?

Express internally raw `http.createServer()` (Module 18) ka hi use karta hai — ye us par ek **routing layer** aur **middleware pipeline** (next chapter) add karta hai. Jab request aati hai, Express registered routes ko order mein check karta hai (URL pattern aur method match karke) aur matching handler ko call karta hai `req`/`res` objects ke saath jo raw `http` ke objects se enhanced hain (extra convenience methods jaise `res.json()`, `req.params`).

## 10. Common Mistakes

- `express.json()` middleware add karna bhool jaana aur `req.body` ke `undefined` hone se confuse hona.
- Route order ko galat samajhna — Express routes ko **registration order mein** match karta hai, isliye catch-all/404 handlers hamesha **last** mein hone chahiye.
- `req.params` aur `req.query` ko confuse karna.

## 11. Edge Cases

```js
app.get("/users/:id", (req, res) => { res.send("Specific user"); });
app.get("/users/new", (req, res) => { res.send("New user form"); }); // This will NEVER match!
// Because "/users/:id" matches "/users/new" first (registered earlier) — "new" gets captured as :id
// Fix: register more specific routes BEFORE dynamic ones
```

## 12. Real-World Usage

Express (aur similar frameworks jaise Fastify, NestJS) almost universal hain Node.js backend development mein. Ye course Express ko primary example ki tarah use karega baaki backend concepts (middleware, auth, error handling) explain karne ke liye.

## 13. Comparison With Related Concepts

**⚠️ More effort, more control:** Raw `http` module (Module 18).
**✅ Modern / Recommended for real projects:** Express (ya similar framework) — cleaner syntax, ecosystem of middleware, faster development.

## 14. Practice Questions

1. `req.params` aur `req.query` mein kya difference hai?
2. Route registration order kyun matter karta hai Express mein?
3. `express.json()` middleware kya karta hai?

## 15. Challenge

Ek Express server banao jisme routes hon: `GET /` (welcome message), `GET /books/:id` (fictional book data return kare based on id), `GET /books?genre=fiction` (query param se filter simulate kare), aur ek 404 catch-all handler.

## 16. Interview Questions

**Q1: Express raw Node.js `http` module ke comparison mein kya advantages deta hai?**
A: Express routing ko **declarative aur clean** banata hai (`app.get("/users/:id", handler)` vs manual `if (req.url === ...)` checks). Ye built-in conveniences deta hai jaise automatic JSON body-parsing (`express.json()`), route parameters (`req.params`), query parameters (`req.query`), aur response helpers (`res.json()`, `res.status()`). Ye **middleware pipeline** (next chapter) bhi enable karta hai jo cross-cutting concerns (logging, authentication, error-handling) ko modular tareeke se handle karne deta hai. Neeche same raw `http` module hi kaam kar raha hota hai — Express usse ek productive abstraction deta hai.

**Q2: Express mein route registration order kyun important hai?**
A: Express incoming request ko **registered routes ke order mein sequentially match** karta hai — jaise hi ek matching route milta hai, uska handler execute hota hai (baaki routes check hi nahi hote, unless `next()` explicitly call ho, next chapter mein detail). Isliye agar ek generic/dynamic route (jaise `/users/:id`) ek specific route (jaise `/users/new`) se **pehle** registered hai, dynamic route pehle match ho jaayega aur specific route kabhi reach nahi hoga. Best practice hai specific routes ko dynamic routes se pehle, aur catch-all/404 handlers ko sabse last mein register karna.

## 17. Chapter Summary

Express Node.js ka mainstream web framework hai jo raw `http` module ko clean routing syntax ke saath enhance karta hai. Route parameters (`:id`) dynamic segments capture karte hain, query parameters (`?key=value`) optional data carry karte hain. Route-registration order matching-behavior ko affect karta hai — specific routes dynamic routes se pehle honi chahiye.

## 18. Revision Checklist

- [ ] Basic Express routes (GET, with params/query) likh sakta hoon.
- [ ] `req.params` vs `req.query` ka difference clear hai.
- [ ] Route-ordering ka importance samajh gaya.

---

**Next:** [02-Middleware-and-REST-APIs.md](./02-Middleware-and-REST-APIs.md)
