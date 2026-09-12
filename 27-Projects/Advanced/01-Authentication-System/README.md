# Project: Authentication System

**Difficulty:** 🔴 Advanced
**Modules Needed:** 18-19, 23
**Previous:** [Intermediate/07-Form-Validation-Application](../../Intermediate/07-Form-Validation-Application/README.md)
**Next:** [02-REST-API](../02-REST-API/README.md)

---

## 1. Requirements

Ek complete backend authentication-system — register, login, protected-routes, aur token-refresh — production-grade security-practices ke saath.

## 2. Features

- User registration (with password-hashing)
- Login (JWT-issuance)
- Protected routes (middleware-based)
- Logout
- Password-strength enforcement

## 3. Architecture

```
auth-system/
├── server.js
├── package.json
├── .env                    (NEVER commit — Module 18, ch 03; Module 23)
├── .gitignore
├── routes/
│   └── auth.js
├── middleware/
│   └── authenticate.js
└── utils/
    └── users.js            (in-memory store for this project; swap for a real DB — Module 20)
```

## 4. Step-by-Step Build Process

**Step 1 — Setup (Module 18, chapter 01):**
```bash
npm init -y
npm install express bcrypt jsonwebtoken dotenv
```

**Step 2 — `.env` file (Module 18, chapter 03; Module 23, chapter 04):**
```
JWT_SECRET=a-very-long-random-string-never-commit-this
PORT=3000
```

**Step 3 — `utils/users.js` (in-memory user-store):**
```js
const users = []; // In production: replace with a real database (Module 20)

function findUserByEmail(email) {
  return users.find(u => u.email === email);
}

function createUser(email, hashedPassword) {
  const user = { id: users.length + 1, email, password: hashedPassword };
  users.push(user);
  return user;
}

module.exports = { findUserByEmail, createUser };
```

**Step 4 — `middleware/authenticate.js` (Module 19, chapter 02's middleware pattern; Module 19, chapter 03's JWT-verification):**
```js
const jwt = require("jsonwebtoken");

function authenticate(req, res, next) {
  const authHeader = req.headers["authorization"];
  const token = authHeader && authHeader.split(" ")[1];

  if (!token) {
    return res.status(401).json({ error: "Access token required" });
  }

  jwt.verify(token, process.env.JWT_SECRET, (err, decoded) => {
    if (err) return res.status(403).json({ error: "Invalid or expired token" });
    req.user = decoded;
    next();
  });
}

module.exports = authenticate;
```

**Step 5 — `routes/auth.js` (combining Module 19's validation-middleware, Module 19 ch 03's bcrypt/JWT patterns):**
```js
const express = require("express");
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");
const { findUserByEmail, createUser } = require("../utils/users");

const router = express.Router();

function validateSignup(req, res, next) {
  const { email, password } = req.body;
  const errors = [];
  if (!email || !email.includes("@")) errors.push("Valid email required");
  if (!password || password.length < 8) errors.push("Password must be at least 8 characters");
  if (errors.length > 0) return res.status(400).json({ errors });
  next();
}

router.post("/register", validateSignup, async (req, res, next) => {
  try {
    const { email, password } = req.body;
    if (findUserByEmail(email)) {
      return res.status(409).json({ error: "Email already registered" });
    }
    const hashedPassword = await bcrypt.hash(password, 10); // Module 19, ch 03!
    const user = createUser(email, hashedPassword);
    res.status(201).json({ message: "User registered", userId: user.id });
  } catch (error) {
    next(error); // Module 19, chapter 04's async-error-forwarding!
  }
});

router.post("/login", async (req, res, next) => {
  try {
    const { email, password } = req.body;
    const user = findUserByEmail(email);
    if (!user) return res.status(401).json({ error: "Invalid credentials" });

    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) return res.status(401).json({ error: "Invalid credentials" });

    const token = jwt.sign({ userId: user.id, email: user.email }, process.env.JWT_SECRET, { expiresIn: "1h" });
    res.json({ token });
  } catch (error) {
    next(error);
  }
});

module.exports = router;
```

**Step 6 — `server.js` (tying it together, Module 19 ch 04's error-handling):**
```js
require("dotenv").config();
const express = require("express");
const authRoutes = require("./routes/auth");
const authenticate = require("./middleware/authenticate");

const app = express();
app.use(express.json());
app.use("/api/auth", authRoutes);

app.get("/api/profile", authenticate, (req, res) => {
  res.json({ message: `Welcome, user ${req.user.email}!` });
});

app.use((error, req, res, next) => { // Centralized error handler — Module 19, ch 04!
  console.error(error.stack);
  res.status(500).json({ error: "Internal Server Error" });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

## 5. Explanation

- Passwords **never** stored/logged in plain-text — `bcrypt.hash()` (Module 19, chapter 03) one-way-hashes before storage.
- JWT (Module 19, chapter 03) enables **stateless** authentication — `/api/profile` doesn't need a database-lookup to verify the user, just signature-verification.
- `.env` + `dotenv` (Module 18, chapter 03) keeps the JWT-secret out of source-code — critical per Module 23, chapter 04's "never hardcode secrets" principle.

## 6. Debugging

- Agar "Invalid or expired token" hamesha aaye: verify `JWT_SECRET` `.env` mein consistently same hai (sign aur verify dono ke liye).
- Agar registration silently fail ho: check middleware-order — `validateSignup` route-handler se pehle registered hona chahiye.

## 7. Testing

Test with a tool like Postman/curl: register a user, attempt duplicate-registration (should 409), login with correct/incorrect credentials, access `/api/profile` with/without a valid token.

## 8. Improvements

- Add refresh-tokens (short-lived access-token + long-lived refresh-token pattern) for better security/UX balance.
- Add rate-limiting on `/login` (Module 23, chapter 03) to prevent brute-force attacks.
- Replace in-memory `users` array with a real database (Module 20).

## 9. Advanced Version

Add role-based authorization (admin vs regular-user routes), email-verification-flow, and password-reset-via-email — a genuinely production-shaped auth system.

---

**Next:** [02-REST-API](../02-REST-API/README.md)
