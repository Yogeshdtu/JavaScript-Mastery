# Process and Environment Variables

**Module:** 18-NodeJS
**Difficulty:** 🟡 Intermediate
**Previous:** [02-File-System-and-Path.md](./02-File-System-and-Path.md)
**Next:** [04-Events-Streams-and-Buffers.md](./04-Events-Streams-and-Buffers.md)

---

## 1. Learning Objectives

- `process` object ke useful properties/methods samajhna.
- Environment variables ka use samajhna (configuration, secrets).
- Command-line arguments access karna.

## 2. Prerequisites

Module 18, chapters 01-02.

## 3. Concept in Simple Hinglish

`process` ek global object hai Node.js mein jo **current running program** ke baare mein information deta hai aur usse control karne deta hai — command-line arguments, environment variables, exit codes, sab `process` ke through accessible hain. **Environment variables** configuration values hain jo operating-system-level pe set hoti hain, code ke bahar — secrets aur environment-specific settings ke liye perfect.

## 4. Technical Explanation

**Common `process` properties/methods:**
- `process.env`: Environment variables ka object.
- `process.argv`: Command-line arguments ka array.
- `process.exit(code)`: Program ko explicitly terminate karta hai.
- `process.platform`: OS platform (`"win32"`, `"darwin"`, `"linux"`).
- `process.version`: Node.js version.

## 5. Syntax

```js
console.log(process.env.NODE_ENV);
console.log(process.argv);
process.exit(1); // Exit with an error code
```

## 6. Basic Examples

```js
console.log(process.env.NODE_ENV); // "development", "production", or undefined
console.log(process.platform);       // "win32", "linux", "darwin"
console.log(process.version);         // "v20.11.0" (example)
```

## 7. Detailed Examples

**Environment variables — why they're essential for configuration:**
```js
// ⚠️ NEVER hardcode secrets directly in your code!
// const apiKey = "sk_live_abc123xyz"; // Bad practice — exposed in source control!

// ✅ Use environment variables instead
const apiKey = process.env.API_KEY;
const dbUrl = process.env.DATABASE_URL;

if (!apiKey) {
  console.error("Missing API_KEY environment variable!");
  process.exit(1);
}
```
Setting environment variables (terminal example):
```bash
# On Mac/Linux
API_KEY=abc123 node app.js

# On Windows PowerShell
$env:API_KEY="abc123"; node app.js
```

**Using `.env` files with the `dotenv` package (a very common pattern):**
```js
// .env file (never commit this to version control!)
// API_KEY=abc123
// DATABASE_URL=mongodb://localhost:27017/mydb

// app.js
require("dotenv").config(); // Loads .env file into process.env
console.log(process.env.API_KEY); // "abc123"
```
`.env` files ko **`.gitignore`** mein add karna critical hai — inme secrets hote hain jo version control mein publicly expose nahi hone chahiye (Module 23 - Security mein detail).

**Command-line arguments — building CLI tools:**
```js
// If run as: node app.js hello world
console.log(process.argv);
// [
//   '/usr/local/bin/node',      // argv[0] — path to node executable
//   '/path/to/app.js',           // argv[1] — path to the script
//   'hello',                       // argv[2] — first actual argument
//   'world'                        // argv[3] — second actual argument
// ]

const args = process.argv.slice(2); // Skip the first 2 — get just the actual arguments
console.log(args); // ['hello', 'world']
```

**Environment-based configuration — a real-world pattern:**
```js
const config = {
  port: process.env.PORT || 3000,
  environment: process.env.NODE_ENV || "development",
  isProduction: process.env.NODE_ENV === "production"
};

if (config.isProduction) {
  console.log("Running in production mode — verbose logging disabled");
} else {
  console.log("Running in development mode — verbose logging enabled");
}
```

**Graceful process termination:**
```js
process.on("SIGINT", () => { // Triggered by Ctrl+C
  console.log("Shutting down gracefully...");
  // Close database connections, finish pending requests, etc.
  process.exit(0);
});
```

## 8. Mental Model

`process` object ko socho tumhare program ka **"control panel aur dashboard"** — jaha se tum current environment ke baare mein information dekh sakte ho (kaunsa OS, kaunse settings) aur program ko control kar sakte ho (exit karna, signals sunna). Environment variables ko socho **settings jo "building ke bahar" (application code ke bahar) store hoti hain** — different buildings (environments — dev, staging, production) mein different settings ho sakti hain, same code ke saath.

## 9. What Happens Internally?

`process.env` operating system ke environment variables ko ek JavaScript object ki tarah expose karta hai — ye variables shell/terminal se, `.env` files se (dotenv library ke through), ya deployment platform (jaise Heroku, Vercel) ki settings se set ho sakti hain. Node.js process start hote hi in values ko capture kar leta hai.

## 10. Common Mistakes

- Secrets (API keys, passwords) ko directly code mein hardcode karna, environment variables use na karna.
- `.env` files ko accidentally git mein commit kar dena.
- `process.argv` ke indices ko galat samajhna — pehle 2 elements hamesha node-path aur script-path hote hain.

## 11. Edge Cases

```js
console.log(typeof process.env.PORT); // "string" — ALL environment variables are strings, even numbers!
const port = Number(process.env.PORT) || 3000; // Must explicitly convert if you need a number
```

## 12. Real-World Usage

Environment variables production applications mein universal hain — database URLs, API keys, feature-flags, port numbers, sab environment-specific configuration ke through manage hote hain, taaki same codebase different environments (dev/staging/production) mein bina code-change ke deploy ho sake.

## 13. Comparison With Related Concepts

| Approach | Security | Flexibility |
|----------|-------------|----------------|
| Hardcoded values in code | ❌ Poor (exposed in source) | ❌ Poor (need code change to update) |
| Environment variables | ✅ Good (kept out of source control) | ✅ Good (change per environment easily) |

## 14. Practice Questions

1. `process.env` ki saari values kis type ki hoti hain?
2. `.env` files ko `.gitignore` mein kyun add karna chahiye?
3. `process.argv` ke pehle 2 elements kya represent karte hain?

## 15. Challenge

Ek CLI script `greet.js` banao jo command-line argument se naam le (`node greet.js Ansh`) aur `"Hello, Ansh!"` print kare. Agar koi naam na diya jaaye, default "Hello, Guest!" print kare.

## 16. Interview Questions

**Q1: Environment variables use karna secrets/configuration ke liye kyun important hai?**
A: Environment variables secrets (API keys, database credentials) aur environment-specific settings (port numbers, feature flags) ko **application code se separate** rakhte hain. Ye 2 major fayde dete hain: (1) **Security** — secrets source code/version-control mein directly nahi dikhte, accidental exposure (jaise public GitHub repo) ka risk kam hota hai; (2) **Flexibility** — same code bina modification ke different environments (development, staging, production) mein deploy ho sakta hai, sirf environment variables change karke.

**Q2: `process.env` se milne wali values ka type kya hota hai, aur iska practical implication kya hai?**
A: `process.env` ki **saari values strings hoti hain**, chahe wo conceptually numbers ya booleans represent kar rahi hon. Isliye agar tumhe `process.env.PORT` ko number ki tarah use karna hai, explicitly convert karna padega (`Number(process.env.PORT)`), warna string concatenation jaisi unexpected behavior ho sakti hai (jaise `"3000" + 1` = `"30001"`, not `3001`). Similarly boolean-like values (`"true"`/`"false"`) ko explicitly compare karna padta hai (`process.env.DEBUG === "true"`), directly truthy-check nahi kar sakte kyunki non-empty string `"false"` bhi truthy hai.

## 17. Chapter Summary

`process` object current Node.js program ke baare mein information aur control deta hai — `process.env` (environment variables), `process.argv` (CLI arguments), `process.exit()`. Environment variables secrets/configuration ko code se separate rakhte hain, security aur flexibility dono ke liye — `.env` files (with `dotenv`) ek common pattern hai, jinhe `.gitignore` mein rakhna zaroori hai.

## 18. Revision Checklist

- [ ] `process.env`, `process.argv` ka use yaad hai.
- [ ] Environment variables secrets ke liye kyun better hain, samajh gaya.
- [ ] `process.env` values hamesha strings hoti hain, ye edge case yaad hai.

---

**Next:** [04-Events-Streams-and-Buffers.md](./04-Events-Streams-and-Buffers.md)
