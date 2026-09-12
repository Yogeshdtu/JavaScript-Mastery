# Authentication and Validation

**Module:** 19-Backend-JavaScript
**Difficulty:** 🔴 Advanced
**Previous:** [02-Middleware-and-REST-APIs.md](./02-Middleware-and-REST-APIs.md)
**Next:** [04-Error-Handling-Logging-Config.md](./04-Error-Handling-Logging-Config.md)

---

## 1. Learning Objectives

- Password hashing ka concept samajhna (bcrypt jaisi library ke saath).
- JWT (JSON Web Token) based authentication flow samajhna.
- Request validation middleware likhna.

## 2. Prerequisites

Module 19, chapters 01-02. [16-Fetch-and-APIs/04-Auth-CORS-RateLimiting-Pagination.md](../16-Fetch-and-APIs/04-Auth-CORS-RateLimiting-Pagination.md)

## 3. Concept in Simple Hinglish

Backend authentication ka matlab hai — server-side pe verify karna ki user **actually wahi hai jo wo claim kar raha hai**, aur uske baad se requests pe unko identify karna. **Passwords kabhi plain-text mein store nahi hoti** — hamesha "hash" karke store hoti hain. **JWT** ek popular tareeka hai stateless authentication ka.

## 4. Technical Explanation

**Password Hashing:** Ek one-way mathematical function jo password ko ek fixed-length, irreversible string mein convert kar deti hai. `bcrypt` library isके liye industry-standard hai — ye "salting" bhi automatically karti hai (same password ke liye different hash, security ke liye).

**JWT (JSON Web Token):** Ek self-contained token jisme user information (encoded, encrypted nahi by default) hoti hai, aur ek signature jo verify karta hai token tamper nahi hua. Server login pe token issue karta hai, client har request mein wo token bhejta hai (`Authorization` header), server signature verify karke user identify karta hai — **database lookup ki zaroorat nahi** har request pe (stateless).

## 5. Syntax

```js
const bcrypt = require("bcrypt");
const hashedPassword = await bcrypt.hash(plainPassword, 10); // 10 = salt rounds
const isMatch = await bcrypt.compare(plainPassword, hashedPassword);

const jwt = require("jsonwebtoken");
const token = jwt.sign({ userId: 5 }, "secretKey", { expiresIn: "1h" });
const decoded = jwt.verify(token, "secretKey");
```

## 6. Basic Examples

```js
const bcrypt = require("bcrypt");

async function registerUser(password) {
  const hashedPassword = await bcrypt.hash(password, 10);
  console.log(hashedPassword); // Something like: $2b$10$N9qo8uLOickgx2ZMRZoMy...
  return hashedPassword;
}
```

## 7. Detailed Examples

**Complete registration and login flow:**
```js
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");
const express = require("express");
const app = express();
app.use(express.json());

const users = []; // In-memory "database" for this example
const JWT_SECRET = process.env.JWT_SECRET || "dev-secret-change-in-production"; // Module 18, chapter 03!

app.post("/register", async (req, res) => {
  const { email, password } = req.body;

  const hashedPassword = await bcrypt.hash(password, 10); // NEVER store plain-text passwords!
  const user = { id: users.length + 1, email, password: hashedPassword };
  users.push(user);

  res.status(201).json({ message: "User registered", userId: user.id });
});

app.post("/login", async (req, res) => {
  const { email, password } = req.body;

  const user = users.find(u => u.email === email);
  if (!user) {
    return res.status(401).json({ error: "Invalid credentials" });
  }

  const isMatch = await bcrypt.compare(password, user.password); // Compare plain vs hashed
  if (!isMatch) {
    return res.status(401).json({ error: "Invalid credentials" });
  }

  const token = jwt.sign({ userId: user.id, email: user.email }, JWT_SECRET, { expiresIn: "1h" });
  res.json({ token }); // Client stores this token and sends it with future requests
});
```

**Authentication middleware — protecting routes with JWT:**
```js
function authenticateToken(req, res, next) {
  const authHeader = req.headers["authorization"]; // Expected format: "Bearer <token>"
  const token = authHeader && authHeader.split(" ")[1];

  if (!token) {
    return res.status(401).json({ error: "Access token required" });
  }

  jwt.verify(token, JWT_SECRET, (error, decoded) => {
    if (error) {
      return res.status(403).json({ error: "Invalid or expired token" });
    }
    req.user = decoded; // Attach decoded user info to the request for later use
    next();
  });
}

app.get("/profile", authenticateToken, (req, res) => {
  res.json({ message: `Welcome, user ${req.user.userId}!` });
});
```
Ye pattern Module 19, chapter 02 ke middleware concept ka direct real-world application hai — authentication logic ek reusable middleware mein hai, jise kisi bhi "protected" route pe simply add kiya ja sakta hai.

**Input validation middleware — a common pattern:**
```js
function validateRegistration(req, res, next) {
  const { email, password } = req.body;
  const errors = [];

  if (!email || !email.includes("@")) {
    errors.push("Valid email is required");
  }
  if (!password || password.length < 8) {
    errors.push("Password must be at least 8 characters");
  }

  if (errors.length > 0) {
    return res.status(400).json({ errors });
  }
  next();
}

app.post("/register", validateRegistration, async (req, res) => {
  // At this point, we know email and password are valid — safe to proceed
  // ... registration logic
});
```
**Note:** Real-world projects mein validation libraries (jaise `Joi`, `Zod`, `express-validator`) use ki jaati hain jo ye kaam zyada robustly karti hain — but underlying concept (validate before processing) same rehta hai.

## 8. Mental Model

Password hashing ko socho **"ek egg ko omelette banana"** — process irreversible hai, tum omelette se wapas raw egg nahi bana sakte. JWT ko socho ek **tamper-evident sealed envelope** — jo bhi envelope kholta hai, seal (signature) verify kar sakta hai ki kisi ne beech mein content change to nahi kiya, bina kisi central "database check" ke zaroorat.

## 9. What Happens Internally?

`bcrypt.hash()` internally password ko multiple "rounds" (iterations, `10` common value hai) ke through ek complex, slow-by-design algorithm se process karta hai — ye deliberately **slow** hai taaki brute-force attacks impractical ho jaayein. JWT verification signature ko cryptographically check karta hai using the secret key — agar token ka koi bhi part tamper hua ho, signature match nahi karegi.

## 10. Common Mistakes

- Passwords ko plain-text mein store karna — critical security vulnerability (Module 23 mein detail).
- JWT secret key ko hardcode karna code mein — environment variable use karo (Module 18, chapter 03).
- JWT ko "encrypted" samajhna — ye sirf **signed** hai, encoded content readable hai (koi bhi `atob()`/base64-decode se dekh sakta hai), sirf tampering detect hoti hai. Sensitive data JWT payload mein directly mat rakho.

## 11. Edge Cases

Expired JWT tokens `jwt.verify()` ko error throw karwate hain — client ko naya token lena padta hai (re-login ya "refresh token" mechanism, jo advanced topic hai).

## 12. Real-World Usage

Almost saari modern applications (mobile apps, SPAs) JWT-based ya session-based authentication use karti hain. Input validation har production API mein present hoti hai — security aur data-integrity dono ke liye essential.

## 13. Comparison With Related Concepts

| Approach | Statefulness | Common Use |
|----------|----------------|----------------|
| Session-based (server stores session) | Stateful | Traditional web apps |
| JWT-based (token contains info) | Stateless | APIs, SPAs, mobile apps |

## 14. Practice Questions

1. Passwords ko hash karna kyun zaroori hai, sirf encrypt karna kaafi kyun nahi hai?
2. JWT "encrypted" hai ya "signed" — difference kya hai?
3. Authentication middleware kaise route-protection implement karta hai?

## 15. Challenge

Upar diye gaye register/login/authenticateToken pattern ko extend karo — ek naya protected route `GET /orders` banao jo sirf authenticated users ko access karne de, aur `req.user.userId` use karke ek personalized message return kare.

## 16. Interview Questions

**Q1: Passwords ko database mein plain-text ki jagah hash karke kyun store kiya jaata hai?**
A: Agar database breach ho jaaye aur passwords plain-text mein stored hon, attacker ko **turant** har user ka actual password mil jaata hai — jo dusri websites pe bhi use ho sakta hai (password reuse). Hashing ek **one-way** transformation hai — hash se original password wapas nikaalna computationally impractical hai. `bcrypt` jaisi libraries additionally "salting" (per-password random data) use karti hain, taaki same password ke liye bhi different hash bane, aur "rainbow table" attacks (precomputed hash lookups) fail ho jaayein.

**Q2: JWT (JSON Web Token) "stateless" authentication kaise enable karta hai?**
A: JWT mein user-identifying information (jaise `userId`) directly token ke andar **encoded** hoti hai, saath mein ek cryptographic **signature** jo server ke secret key se generate hoti hai. Har request pe, server sirf signature verify karta hai (fast, computation-only operation) — usse **database mein session lookup karne ki zaroorat nahi** padti ye confirm karne ke liye ki user authenticated hai. Ye "stateless" hai kyunki server ko kisi session-state ko yaad rakhne/store karne ki zaroorat nahi — saari zaroori information token mein hi self-contained hai.

## 17. Chapter Summary

Passwords hamesha hash karke store hoti hain (`bcrypt`), kabhi plain-text mein nahi. JWT stateless authentication enable karta hai — signed tokens jo server ko database-lookup ke bina user verify karne dete hain. Authentication middleware protected routes ko reusable tareeke se secure karta hai. Input validation malformed/malicious data ko processing se pehle reject karta hai.

## 18. Revision Checklist

- [ ] Password hashing ka one-way nature aur importance clear hai.
- [ ] JWT ka "signed, not encrypted" distinction yaad hai.
- [ ] Authentication middleware pattern likh sakta hoon.

---

**Next:** [04-Error-Handling-Logging-Config.md](./04-Error-Handling-Logging-Config.md)
