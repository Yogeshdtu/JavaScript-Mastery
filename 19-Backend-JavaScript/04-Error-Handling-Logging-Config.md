# Error Handling, Logging, and Configuration

**Module:** 19-Backend-JavaScript
**Difficulty:** 🟠 Advanced
**Previous:** [03-Authentication-and-Validation.md](./03-Authentication-and-Validation.md)
**Next:** [20-Databases/01-SQL-vs-NoSQL.md](../20-Databases/01-SQL-vs-NoSQL.md)

---

## 1. Learning Objectives

- Centralized error-handling pattern implement karna production-ready backend mein.
- Logging best practices samajhna.
- Environment-based configuration (dev/staging/production) manage karna.

## 2. Prerequisites

Module 19, chapters 01-03.

## 3. Concept in Simple Hinglish

Production-ready backend applications sirf "kaam karna" kaafi nahi hai — unhe **gracefully fail** karna bhi aana chahiye (proper error responses), **track karna** chahiye ki kya ho raha hai (logging), aur **different environments** mein correctly configure hona chahiye (dev vs production settings).

## 4. Technical Explanation

**Centralized Error Handling:** Express mein error-handling middleware (Module 19, chapter 02 se yaad karo — 4-parameter signature) saare errors ko ek jagah handle karta hai, consistent error-response format ensure karta hai.

**Async Error Handling:** `async` route handlers ke errors ko automatically catch karne ke liye wrapper functions ya libraries (jaise `express-async-errors`) use hoti hain — warna unhandled Promise rejections silently fail ho sakti hain.

**Logging Levels:** `error`, `warn`, `info`, `debug` — different severity ki information ko categorize karna.

## 5. Syntax

```js
// Centralized error handler
app.use((error, req, res, next) => {
  console.error(error.stack);
  res.status(error.statusCode || 500).json({ error: error.message });
});

// Async wrapper
const asyncHandler = (fn) => (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next);
```

## 6. Basic Examples

```js
app.use((error, req, res, next) => {
  console.error(error.message);
  res.status(500).json({ error: "Something went wrong" });
});
```

## 7. Detailed Examples

**A custom Error class for structured error handling:**
```js
class AppError extends Error { // Module 10, chapter 02 (classes/extends) applied!
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true; // Distinguishes expected errors from unexpected bugs
  }
}

// Usage in a route
app.get("/products/:id", (req, res, next) => {
  const product = findProduct(req.params.id);
  if (!product) {
    return next(new AppError("Product not found", 404)); // Pass to error middleware
  }
  res.json(product);
});

// Centralized error handler
app.use((error, req, res, next) => {
  const statusCode = error.statusCode || 500;
  const message = error.isOperational ? error.message : "Internal Server Error";

  console.error(`[ERROR] ${statusCode}: ${error.message}`);
  res.status(statusCode).json({ error: message });
});
```
**Why hide the real message for non-operational errors?** Agar ek unexpected bug (jaise database connection failure) hoti hai, uska raw error message client ko dikhana **security risk** ho sakta hai (internal system details expose ho sakti hain) — Module 23 mein detail. Isi liye "operational" (expected, jaise "not found") errors ka message safe hai dikhana, "non-operational" (unexpected bugs) ka generic message better hai.

**Handling async errors — the common pitfall and fix:**
```js
// ⚠️ Problem — async errors DON'T automatically go to Express's error handler (in older Express versions)!
app.get("/risky", async (req, res, next) => {
  const data = await someAsyncOperationThatMightFail(); // If this throws, it's an UNHANDLED rejection!
  res.json(data);
});

// ✅ Fix — wrap async handlers to catch errors and pass them to next()
function asyncHandler(fn) {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
}

app.get("/risky", asyncHandler(async (req, res) => {
  const data = await someAsyncOperationThatMightFail();
  res.json(data); // If this throws, asyncHandler catches it and passes to error middleware
}));
```
**Note:** Express 5 (newer versions) automatically handle async errors — but understanding this pattern is important for existing codebases (Express 4 and earlier) and for understanding *why* the problem exists.

**Structured logging — beyond just `console.log`:**
```js
function log(level, message, meta = {}) {
  const timestamp = new Date().toISOString();
  console.log(JSON.stringify({ timestamp, level, message, ...meta }));
}

log("info", "Server started", { port: 3000 });
log("error", "Database connection failed", { error: "ECONNREFUSED" });
// In real production apps, dedicated libraries (Winston, Pino) provide this + file/remote logging
```

**Environment-based configuration (connecting to Module 18, chapter 03):**
```js
const config = {
  development: {
    dbUrl: "mongodb://localhost:27017/dev",
    logLevel: "debug"
  },
  production: {
    dbUrl: process.env.DATABASE_URL,
    logLevel: "error"
  }
};

const env = process.env.NODE_ENV || "development";
const currentConfig = config[env];

console.log(`Running in ${env} mode with log level: ${currentConfig.logLevel}`);
```

## 8. Mental Model

Centralized error handling ko socho ek **hospital ka emergency room** — chahe patient ko kuch bhi ho (koi bhi type ka error), sab ek hi centralized jagah (error middleware) triage hote hain, consistent process follow karte hue. Environment configuration ko socho **ek hi recipe, different kitchens (environments) ke liye adjusted quantities** — same code, different settings depending on kaha "cook" ho raha hai.

## 9. What Happens Internally?

Express ka error-handling middleware tab trigger hota hai jab koi `next(error)` call kare (explicitly, ya automatically synchronous throws ke case mein). Async functions ke throws automatically `next()` tak nahi pahunchte (Promise rejections hain, exceptions nahi) — isi liye wrapper functions ya newer Express versions ka built-in support zaroori hai.

## 10. Common Mistakes

- Async route handlers mein errors ko properly catch/forward na karna.
- Raw internal error messages (jaise database stack traces) ko directly client ko expose karna.
- `console.log` ko production logging solution ki tarah treat karna — real applications structured logging libraries use karti hain jo file/remote-storage support karti hain.

## 11. Edge Cases

Uncaught exceptions (jo kahi bhi catch nahi hui) aur unhandled Promise rejections poore Node.js process ko crash kar sakte hain — production apps `process.on("uncaughtException", ...)` aur `process.on("unhandledRejection", ...)` ke saath global safety-nets rakhte hain (though best practice hai inhe properly handle karna, in global handlers pe depend na karna).

## 12. Real-World Usage

Har production backend application ye 3 concerns (error-handling, logging, configuration) implement karti hai. Dedicated tools: **Winston/Pino** (logging), **Sentry** (error-tracking/monitoring), **dotenv** (configuration).

## 13. Comparison With Related Concepts

| Concern | Development Approach | Production Approach |
|---------|--------------------------|--------------------------|
| Error messages | Detailed, verbose | Generic (for unexpected errors) |
| Logging | Console, verbose | Structured, persisted, external service |
| Configuration | Simple defaults | Environment variables, secrets management |

## 14. Practice Questions

1. Async route handler errors automatically Express error-middleware tak kyun nahi pahunchte (older Express)?
2. Operational aur non-operational errors mein kya difference hai?
3. Production mein raw error messages client ko show karna kyun risky hai?

## 15. Challenge

Ek custom `AppError` class banao (upar diye pattern jaisi), 2 routes banao jo different scenarios mein isse throw karein (`404` for not-found, `400` for bad-input), aur ek centralized error-handling middleware banao jo consistently format kare responses.

## 16. Interview Questions

**Q1: Express mein async route handlers ke errors properly handle karna challenging kyun hai (especially older versions mein)?**
A: Express ka built-in error-handling (`next(error)` ka automatic trigger) synchronous code ke throws ke liye designed tha. Jab ek `async` function ke andar error throw hoti hai, wo actually ek **rejected Promise** create karti hai — Express (older versions, pre-5) is rejection ko automatically catch nahi karta aur error-handling middleware tak forward nahi karta. Isse "unhandled promise rejection" ban sakti hai jo silently fail ho jaati hai ya process ko crash kar sakti hai. Fix hai async handlers ko ek wrapper function mein wrap karna jo `.catch(next)` ke through errors ko manually forward kare, ya newer Express versions (5+) use karna jo isse automatically handle karte hain.

**Q2: Production mein raw/internal error messages ko client ko directly expose karna kyun avoid karna chahiye?**
A: Internal error messages (jaise database connection strings, stack traces, internal file paths) mein **sensitive system information** ho sakti hai jo attackers ke liye useful ho sakti hai application ki internal architecture samajhne mein, ya vulnerabilities dhundne mein (Module 23 - Security se directly related). Best practice hai "operational" (expected, user-facing) errors ka meaningful message dikhana (jaise "Product not found"), lekin "non-operational" (unexpected bugs/system failures) ke liye ek **generic message** ("Internal Server Error") dikhana, jabki actual detailed error internally log kiya jaaye developers ke liye.

## 17. Chapter Summary

Centralized error-handling middleware consistent error responses ensure karta hai. Async errors ko properly `next()` tak forward karna zaroori hai (wrapper functions se). Custom Error classes (`AppError`) operational vs unexpected errors ko differentiate karte hain. Structured logging aur environment-based configuration production-readiness ke essential parts hain.

## 18. Revision Checklist

- [ ] Centralized error-handling pattern likh sakta hoon.
- [ ] Async-error-handling ka challenge aur fix samajh gaya.
- [ ] Operational vs non-operational error distinction clear hai.

---

**Module 19 Complete!** Next Module: [20-Databases/01-SQL-vs-NoSQL.md](../20-Databases/01-SQL-vs-NoSQL.md)
