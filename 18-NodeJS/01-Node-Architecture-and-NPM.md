# Node.js Architecture and NPM

**Module:** 18-NodeJS
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [17-Modules/03-ESM-vs-CommonJS-and-Dynamic-Imports.md](../17-Modules/03-ESM-vs-CommonJS-and-Dynamic-Imports.md)
**Next:** [02-File-System-and-Path.md](./02-File-System-and-Path.md)

---

## 1. Learning Objectives

- Node.js ka architecture (V8 + libuv) samajhna.
- `package.json`, `package-lock.json`, dependencies ka role samajhna.
- npm scripts aur common npm commands use karna.

## 2. Prerequisites

Module 01, chapter 03 (Browser vs Node runtime). Node.js installed hona chahiye is module ke practical exercises ke liye.

## 3. Concept in Simple Hinglish

**Node.js** JavaScript ko **browser ke bahar** chalane ka runtime environment hai — servers, CLI tools, scripts banane ke liye. **NPM (Node Package Manager)** wo tool hai jo humein dusre developers ke banaye packages (libraries) install/manage karne deta hai, aur apne project ke dependencies track karne deta hai.

## 4. Technical Explanation

**Node.js Architecture:**
- **V8 Engine:** Same JavaScript engine jo Chrome use karta hai — JavaScript code execute karta hai.
- **libuv:** Ek C library jo Node.js ko **non-blocking I/O** (file system, networking) provide karti hai — Web APIs ka Node.js equivalent (Module 15, chapter 01 se yaad karo).
- **Node.js Bindings:** V8 aur libuv ko JavaScript APIs (`fs`, `http`, `process`) ke through connect karti hain.

**`package.json`:** Project ka "identity card" — naam, version, dependencies, scripts sab define karta hai.

**`package-lock.json`:** Exact installed versions ka detailed record — team members/deployments mein **consistent** dependency versions ensure karta hai.

## 5. Syntax

```bash
npm init                    # Create a new package.json
npm install <package>        # Install and add to dependencies
npm install <package> --save-dev  # Install as a dev dependency
npm run <script-name>         # Run a custom script
node app.js                    # Run a JavaScript file with Node
```

## 6. Basic Examples

```json
// package.json
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.18.0"
  }
}
```

## 7. Detailed Examples

**Understanding `package.json` structure in detail:**
```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "A sample Node.js app",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "node --watch index.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^7.0.0"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "nodemon": "^3.0.0"
  }
}
```
- **`dependencies`:** Packages zaroori hain application ko **actually run** karne ke liye (production).
- **`devDependencies`:** Packages sirf **development/testing** ke liye zaroori hain (jaise testing frameworks) — production mein zaroori nahi.

**Semantic Versioning (SemVer) — understanding version numbers:**
```
^4.18.2
│ │  │ └── Patch version (bug fixes)
│ │  └──── Minor version (new features, backward-compatible)
│ └─────── Major version (breaking changes)
└───────── Caret (^) allows updates to minor/patch, but NOT major
```
- `^4.18.2` → allows `4.x.x` (any minor/patch update within major version 4).
- `~4.18.2` → allows `4.18.x` (only patch updates).
- `4.18.2` (no symbol) → exact version only.

**`npm scripts` — automating common tasks:**
```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "node --watch index.js",
    "build": "webpack --mode production",
    "test": "jest --coverage",
    "lint": "eslint ."
  }
}
```
```bash
npm start        # Special script — can be run without "run"
npm run dev       # Regular scripts need "run"
npm test           # "test" is also a special shortcut
```

**Why `package-lock.json` matters — reproducible installs:**
```
Without package-lock.json:
  "^4.18.2" could install 4.18.2 today, but 4.19.0 next week (different behavior possible!)

With package-lock.json:
  Exact version (e.g., 4.18.2) is LOCKED and recorded — everyone on the team,
  and every deployment, gets the EXACT SAME dependency tree.
```
**⚠️ Important practice:** `package-lock.json` ko **hamesha commit karo** version control mein — ye team-wide consistency ensure karta hai.

**`node_modules` — what it actually is:**
```
node_modules/          <- Contains ALL installed packages AND their own dependencies
├── express/
├── mongoose/
└── ... (potentially hundreds of nested dependencies)
```
`node_modules` folder ko **kabhi commit nahi karna chahiye** version control mein (bahut bada, aur `package.json` + `package-lock.json` se hi regenerate ho sakta hai `npm install` se) — `.gitignore` mein add karna standard practice hai.

## 8. Mental Model

Node.js ko socho ek **car** — V8 engine "engine" hai jo power generate karta hai, libuv "transmission system" hai jo power ko wheels tak (I/O operations tak) pahunchata hai bina engine ko stall kiye. NPM ko socho ek **hardware store** jaha se tum pre-built parts (packages) le sakte ho apni car mein add karne ke liye, `package.json` us car ka "parts list/manual" hai.

## 9. What Happens Internally?

Jab `npm install` chalta hai, npm `package.json` padhta hai, dependencies (aur unki dependencies, recursively) resolve karta hai, unhe `node_modules` mein download karta hai, aur `package-lock.json` ko exact-resolved-versions ke saath update/create karta hai. Jab `node app.js` chalta hai, Node.js runtime V8 ke through code parse/execute karta hai, aur jab bhi `fs`/`http` jaisi APIs use hoti hain, libuv unhe non-blocking tareeke se handle karta hai.

## 10. Common Mistakes

- `node_modules` ko git mein commit karna.
- `package-lock.json` ko ignore/delete karna, jisse different environments mein different dependency versions install ho sakti hain.
- `dependencies` aur `devDependencies` mein galat jagah packages daalna.

## 11. Edge Cases

`npm ci` (continuous integration) command `package-lock.json` ko **exactly** follow karta hai (kisi bhi version-range flexibility ke bina) aur fresh install karta hai — CI/CD pipelines mein `npm install` se zyada reliable/faster hai.

## 12. Real-World Usage

Har Node.js project `package.json`/`npm` use karta hai dependency management ke liye. Scripts (`npm run dev`, `npm test`) daily development workflow ka core hain. Semantic versioning samajhna dependency-upgrade decisions ke liye critical hai.

## 13. Comparison With Related Concepts

| File | Purpose |
|------|---------|
| `package.json` | Project metadata, dependencies list (with version ranges), scripts |
| `package-lock.json` | Exact resolved versions — ensures consistency |
| `node_modules/` | Actual installed package code (not committed to git) |

## 14. Practice Questions

1. `dependencies` aur `devDependencies` mein kya difference hai?
2. `^4.18.2` version range ka matlab kya hai?
3. `package-lock.json` ko commit karna kyun important hai?

## 15. Challenge

Ek naya Node.js project setup karo (`npm init -y`), koi ek package install karo (jaise `chalk` colored console output ke liye), aur ek `npm script` banao jo ek simple `index.js` file run kare jo `chalk` use karke colored text print kare.

## 16. Interview Questions

**Q1: `dependencies` aur `devDependencies` mein kya difference hai `package.json` mein?**
A: `dependencies` wo packages hain jo application ko **production mein actually run** karne ke liye zaroori hain — jaise `express` (server framework), `mongoose` (database library). `devDependencies` wo packages hain jo sirf **development ya testing ke dauraan** zaroori hain, production runtime mein nahi — jaise `jest` (testing), `eslint` (linting), `nodemon` (dev-server auto-restart). Jab production deployment ke liye `npm install --production` (ya `NODE_ENV=production npm install`) chalate hain, `devDependencies` skip ho jaati hain.

**Q2: `package-lock.json` ka purpose kya hai aur ise commit karna kyun important hai?**
A: `package.json` mein dependency versions **ranges** ki tarah specify hoti hain (jaise `^4.18.2`, jo `4.x.x` allow karta hai) — matlab different times pe `npm install` chalane se slightly different (but compatible) versions install ho sakti hain. `package-lock.json` **exact resolved versions** (poori dependency tree ke saath, including nested dependencies) ko record karta hai. Ise commit karna ensure karta hai ki team ke sabhi members, aur production deployment, **exactly same** dependency versions use karein — "works on my machine" jaise issues avoid karta hai.

## 17. Chapter Summary

Node.js V8 (JavaScript execution) aur libuv (non-blocking I/O) ka combination hai — browser ke bahar JavaScript chalane ke liye. NPM dependency management provide karta hai `package.json` (metadata + version ranges) aur `package-lock.json` (exact versions, consistency) ke through. `npm scripts` common tasks automate karte hain.

## 18. Revision Checklist

- [ ] Node.js architecture (V8 + libuv) ka basic idea clear hai.
- [ ] `dependencies` vs `devDependencies` ka difference yaad hai.
- [ ] `package-lock.json` ki importance samajh gayi.

---

**Next:** [02-File-System-and-Path.md](./02-File-System-and-Path.md)
