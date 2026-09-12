# Middleware and REST APIs

**Module:** 19-Backend-JavaScript
**Difficulty:** 🔴 Advanced
**Previous:** [01-HTTP-Servers-and-Routing.md](./01-HTTP-Servers-and-Routing.md)
**Next:** [03-Authentication-and-Validation.md](./03-Authentication-and-Validation.md)

---

## 1. Learning Objectives

- Middleware ka concept samajhna aur custom middleware likhna.
- `next()` function ka exact role samajhna.
- Ek complete REST API endpoint structure banana (Module 16, chapter 03 ke concepts ko implement karke).

## 2. Prerequisites

[01-HTTP-Servers-and-Routing.md](./01-HTTP-Servers-and-Routing.md), [16-Fetch-and-APIs/03-REST-and-CRUD.md](../16-Fetch-and-APIs/03-REST-and-CRUD.md)

## 3. Concept in Simple Hinglish

**Middleware** ek function hai jo request aur response ke **beech mein** chalta hai — route handler tak pahunchne se pehle. Ye logging, authentication, validation, jaisi common cheezein har request pe centrally apply karne deta hai, har route mein baar-baar repeat kiye bina.

## 4. Technical Explanation

Middleware function ka signature: `(req, res, next) => {}`. Ye 3 cheezo mein se kuch kar sakta hai:
1. `req`/`res` ko modify kar sakta hai.
2. Response bhej sakta hai (chain yahi khatam ho jaati hai).
3. `next()` call karke control **agle middleware/route-handler ko pass** kar sakta hai.

Agar `next()` call nahi hoti (aur response bhi nahi bheja jaata), request **hang** ho jaati hai forever.

## 5. Syntax

```js
app.use((req, res, next) => {
  // do something
  next(); // Pass control forward
});

app.get("/path", middleware1, middleware2, (req, res) => {
  res.send("Final handler");
});
```

## 6. Basic Examples

```js
function logger(req, res, next) {
  console.log(`${req.method} ${req.url} — ${new Date().toISOString()}`);
  next(); // MUST call this, or the request hangs forever!
}

app.use(logger); // Applied to ALL routes
```

## 7. Detailed Examples

**Multiple middleware in sequence — the "pipeline" concept:**
```js
function requestLogger(req, res, next) {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
}

function checkApiKey(req, res, next) {
  const apiKey = req.headers["x-api-key"];
  if (!apiKey) {
    return res.status(401).json({ error: "API key required" }); // Chain stops here — next() not called
  }
  next(); // Valid — continue to the next middleware/handler
}

app.use(requestLogger);      // Applies to ALL routes
app.get("/data", checkApiKey, (req, res) => { // Applies ONLY to this specific route
  res.json({ data: "Secret data" });
});
```
**Execution order for `GET /data` (with valid API key):**
```
1. requestLogger runs → logs the request → calls next()
2. checkApiKey runs → validates the key → calls next()
3. Final route handler runs → sends the response
```
**With INVALID API key:** `requestLogger` runs → `checkApiKey` runs → sends 401 response → **route handler NEVER runs** (chain stopped).

**Error-handling middleware — a special 4-parameter signature:**
```js
app.get("/risky", (req, res, next) => {
  try {
    throw new Error("Something broke!");
  } catch (error) {
    next(error); // Passing an error to next() skips to error-handling middleware
  }
});

// Error-handling middleware — Express recognizes it by its 4 parameters
app.use((error, req, res, next) => {
  console.log("Error caught:", error.message);
  res.status(500).json({ error: "Internal Server Error" });
});
```

**A complete REST API — combining everything from Module 16, chapter 03:**
```js
const express = require("express");
const app = express();
app.use(express.json());

let products = [{ id: 1, name: "Laptop", price: 50000 }];
let nextId = 2;

app.get("/products", (req, res) => {
  res.json(products);
});

app.get("/products/:id", (req, res) => {
  const product = products.find(p => p.id === Number(req.params.id));
  if (!product) return res.status(404).json({ error: "Not found" });
  res.json(product);
});

app.post("/products", (req, res) => {
  const newProduct = { id: nextId++, ...req.body };
  products.push(newProduct);
  res.status(201).json(newProduct);
});

app.patch("/products/:id", (req, res) => {
  const product = products.find(p => p.id === Number(req.params.id));
  if (!product) return res.status(404).json({ error: "Not found" });
  Object.assign(product, req.body); // Module 07, chapter 03 concept applied here!
  res.json(product);
});

app.delete("/products/:id", (req, res) => {
  products = products.filter(p => p.id !== Number(req.params.id));
  res.status(204).send();
});

app.listen(3000, () => console.log("API running on port 3000"));
```

## 8. Mental Model

Middleware ko socho **airport security checkpoints** ki tarah — har passenger (request) multiple checkpoints (middleware) se guzarta hai apni final gate (route handler) tak pahunchne se pehle: ID check (logging), security scan (authentication), baggage check (validation). Agar koi checkpoint fail ho jaata hai, passenger aage nahi badh sakta (`next()` call nahi hoti) — usko wahi rok diya jaata hai (error response).

## 9. What Happens Internally?

Express internally middleware ko ek **ordered array/pipeline** ki tarah maintain karta hai. Jab request aati hai, Express is pipeline ko sequentially execute karta hai — har middleware ko `req`, `res`, aur `next` (agle pipeline-item ko call karne wala function) pass karta hai. Ye pattern Module 05, chapter 04 ke "higher-order functions" aur callbacks se conceptually related hai.

## 10. Common Mistakes

- Middleware ke andar `next()` call karna bhool jaana — request hang ho jaati hai.
- `next()` ke baad bhi response bhejne ki koshish karna (jaise `next(); res.send(...)`) — "Cannot set headers after they are sent" error.
- Error-handling middleware ko sahi jagah (baaki sab routes/middleware ke **baad**) register na karna.

## 11. Edge Cases

```js
app.use((req, res, next) => {
  console.log("Before");
  next();
  console.log("After"); // This runs AFTER the rest of the pipeline completes (if synchronous)!
});
```
Middleware ke andar `next()` ke baad ka code bhi chal sakta hai (jaise cleanup logic) — ye ek advanced pattern hai jo `next()` ke "callback jaisa" nature ko dikhata hai.

## 12. Real-World Usage

Middleware authentication (Module 19, chapter 03), request-logging, rate-limiting (Module 16, chapter 04 se connected), CORS-headers-setting, aur input-validation ke liye universal hai professional backend applications mein.

## 13. Comparison With Related Concepts

| Concept | Relation |
|---------|----------|
| Middleware | Runs BEFORE route handlers, for cross-cutting concerns |
| Route handler | The FINAL function that sends the actual response |
| `next()` | Passes control forward in the pipeline |

## 14. Practice Questions

1. `next()` call na karne se kya hota hai?
2. Error-handling middleware ko regular middleware se kaise identify karte hain (signature ke through)?
3. Middleware ka ek real-world use-case batao.

## 15. Challenge

Ek middleware `validateProduct` likho jo check kare `req.body` mein `name` aur `price` dono present hain — agar nahi, `400` error response bheje; agar haan, `next()` call kare. Isse `POST /products` route pe apply karo.

## 16. Interview Questions

**Q1: Express middleware kya hai aur `next()` function ka role kya hai?**
A: Middleware ek function hai signature `(req, res, next)` ke saath, jo incoming request ko route handler tak pahunchne se pehle process karta hai. Ye request/response objects ko modify kar sakta hai, response khud bhej sakta hai (chain terminate karke), ya `next()` call karke control **agle middleware/handler** ko pass kar sakta hai. `next()` critical hai — agar ye call nahi hoti (aur response bhi nahi bheja jaata), request indefinitely "hang" ho jaati hai, kyunki Express ko pata nahi chalta aage kya karna hai.

**Q2: Express mein error-handling middleware regular middleware se kaise different hoti hai?**
A: Error-handling middleware ka signature **4 parameters** rakhta hai: `(error, req, res, next)` — jabki regular middleware sirf 3 (`req, res, next`) rakhta hai. Express is parameter-count se automatically identify karta hai ki ye error-handler hai. Jab koi middleware/route-handler `next(error)` call karta hai (ek argument ke saath), Express normal pipeline ko skip karke seedha nearest error-handling middleware ko us error ke saath call karta hai. Best practice hai error-handling middleware ko **saare regular routes/middleware ke baad** register karna.

## 17. Chapter Summary

Middleware `(req, res, next)` signature wale functions hain jo request-response cycle ke beech chalte hain — logging, auth, validation jaise cross-cutting concerns ke liye. `next()` control ko pipeline mein aage badhata hai. Error-handling middleware ka special 4-parameter signature hota hai. In concepts ko combine karke complete REST APIs (Module 16, chapter 03 ke CRUD patterns) build ki ja sakti hain.

## 18. Revision Checklist

- [ ] Middleware ka `(req, res, next)` pattern aur `next()` ki importance clear hai.
- [ ] Error-handling middleware ka special signature yaad hai.
- [ ] Complete CRUD REST API Express mein likh sakta hoon.

---

**Next:** [03-Authentication-and-Validation.md](./03-Authentication-and-Validation.md)
