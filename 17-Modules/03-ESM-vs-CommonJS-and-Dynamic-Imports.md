# ESM vs CommonJS, and Module Resolution

**Module:** 17-Modules
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [02-CommonJS.md](./02-CommonJS.md)
**Next:** [18-NodeJS/01-Node-Architecture-and-NPM.md](../18-NodeJS/01-Node-Architecture-and-NPM.md)

---

## 1. Learning Objectives

- ESM aur CommonJS ke sabhi key differences ko ek jagah compare karna.
- Node.js mein dono systems configure/detect karna.
- Module resolution ka basic mechanism samajhna.

## 2. Prerequisites

Module 17, chapters 01-02.

## 3. Concept in Simple Hinglish

Ab jab humne dono module systems individually seekh liye hain, is chapter mein hum unhe **side-by-side compare** karenge aur samjhenge ki Node.js project mein kaunsa system use ho raha hai kaise pata karte hain, aur "module resolution" (Node.js kaise decide karta hai kaunsi file load karni hai) kaise kaam karta hai.

## 4. Technical Explanation

**Complete Comparison:**

| Feature | CommonJS ⚠️ Legacy | ES Modules ✅ Modern |
|---------|------------------------|---------------------------|
| Import syntax | `require()` | `import` |
| Export syntax | `module.exports` | `export`/`export default` |
| Loading | Synchronous | Asynchronous (can be) |
| Top-level `await` | ❌ No | ✅ Yes (ES2022) |
| Static analysis | ❌ No (dynamic, runtime) | ✅ Yes (structure known before execution) |
| Strict mode | Manual (`"use strict"`) | Automatic |
| Browser support | ❌ No (needs bundler) | ✅ Yes (native, `type="module"`) |
| File extension (Node.js) | `.js` (default) or `.cjs` | `.mjs` or `.js` (with config) |

**How Node.js decides which system to use:**
```json
// package.json
{
  "type": "module" // Makes .js files use ES Modules by default
  // If omitted or "commonjs", .js files use CommonJS by default
}
```

## 5. Syntax

```js
// Explicit file extensions override package.json setting:
// file.mjs  → always ES Module
// file.cjs  → always CommonJS
```

## 6. Basic Examples

```js
// package.json: { "type": "module" }

// math.js (treated as ES Module because of package.json)
export function add(a, b) { return a + b; }
```

## 7. Detailed Examples

**Using ESM in Node.js — practical setup:**
```json
// package.json
{
  "name": "my-app",
  "type": "module"
}
```
```js
// index.js (now an ES Module)
import { readFile } from "fs/promises"; // Node's built-in modules also support ESM imports

async function main() {
  const data = await readFile("./data.txt", "utf-8"); // Top-level await also works in ESM!
  console.log(data);
}
main();
```

**Interop — using CommonJS from ESM (one-directional support):**
```js
// You CAN import a CommonJS module from an ES Module:
import someCommonJsPackage from "some-old-package"; // Works, Node.js handles the interop

// But you generally CANNOT use `require()` inside an ES Module file directly
// (without extra setup, since `require` isn't defined in ESM scope by default)
```

**Module resolution — how Node.js finds files:**
```js
import { helper } from "./utils"; // Node.js looks for: ./utils.js, ./utils/index.js, etc.
import express from "express";     // Node.js looks in: ./node_modules/express/
```
**Resolution order (simplified) for `require("./utils")` or `import "./utils"`:**
1. Exact file match (`./utils.js`).
2. Directory with an `index.js` (`./utils/index.js`).
3. For bare specifiers (no `./` prefix, jaise `"express"`), Node.js `node_modules` folder mein search karta hai, current directory se upward traversing karte hue.

**A practical migration tip — converting CommonJS to ESM:**
```js
// ⚠️ Before (CommonJS)
const fs = require("fs");
module.exports = { readConfig };
function readConfig() { /* ... */ }

// ✅ After (ES Modules)
import fs from "fs";
export function readConfig() { /* ... */ }
```

## 8. Mental Model

CommonJS aur ESM ko socho **do alag "operating systems"** jo dono JavaScript files chala sakte hain, but apne apne rules ke saath — jaise Windows aur Mac dono computer chalate hain but file-systems, commands thode different hain. `package.json` ka `"type"` field ye decide karta hai "kaunsa OS use ho raha hai is project mein".

## 9. What Happens Internally?

Node.js file extension (`.mjs`/`.cjs`) ya `package.json` ke `"type"` field ke basis pe decide karta hai file ko kaise parse/execute karna hai. ES Modules ke liye, Node.js pehle **static analysis** karta hai (saare `import` statements dhundhta hai) before actually executing, taaki dependency-graph pehle se pata ho — CommonJS mein ye dynamic hai (`require()` calls conditionally ho sakte hain, runtime pe hi pata chalta hai).

## 10. Common Mistakes

- ESM aur CommonJS syntax ko galti se mix karna bina proper configuration ke.
- `package.json` mein `"type": "module"` set karna aur purane CommonJS-style code ko bina update kiye chalane ki koshish karna.
- Module resolution errors (file not found) ko debug karte waqt extension/index.js conventions bhool jaana.

## 11. Edge Cases

Kuch npm packages **dual-mode** support karte hain (both CommonJS and ESM) using `package.json`'s `"exports"` field with conditional entries — ye advanced package-authoring topic hai but real-world packages mein common hai.

## 12. Real-World Usage

Naye Node.js projects (2020s onwards) generally ES Modules ki taraf shift ho rahe hain. Frontend build tools (Vite, Webpack) internally ESM syntax ko heavily leverage karte hain tree-shaking (unused code removal) ke liye — ye static-analysis-friendly nature ki wajah se possible hai, jo CommonJS ke saath utna effective nahi hota.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended for new projects:** ES Modules — better tooling support, native browser compatibility, future-proof.
**⚠️ Legacy but unavoidable:** CommonJS — bahut sara existing ecosystem (npm packages, older codebases) isi pe based hai.

## 14. Practice Questions

1. `package.json` mein `"type": "module"` set karne ka kya effect hota hai?
2. `.mjs` aur `.cjs` extensions ka purpose kya hai?
3. Node.js `require("./utils")` ke liye kaunsi files check karta hai resolution order mein?

## 15. Challenge

Ek naya Node.js project setup karo (`package.json` mein `"type": "module"` set karke), aur ek simple 2-file ES-Module-based application banao (`utils.mjs`/`.js` aur `app.js`) jo `import`/`export` use kare.

## 16. Interview Questions

**Q1: Node.js kaise decide karta hai ki ek `.js` file CommonJS hai ya ES Module?**
A: By default, Node.js `.js` files ko **CommonJS** treat karta hai, jab tak `package.json` mein `"type": "module"` explicitly set na ho — us case mein saari `.js` files ES Modules treat hoti hain. Explicit file extensions is default ko override kar sakte hain: `.mjs` **hamesha** ES Module hai, `.cjs` **hamesha** CommonJS hai, chahe `package.json` mein kuch bhi set ho.

**Q2: ES Modules CommonJS se better static-analysis-friendly kyun hain, aur iska practical benefit kya hai?**
A: ES Modules mein `import`/`export` statements **top-level aur static** hone chahiye (conditionally ya dynamically nahi likhe ja sakte, `import()` function ko chhodkar) — isliye tools bina code actually run kiye poori dependency-tree determine kar sakte hain. CommonJS mein `require()` kahi bhi, conditionally call ho sakta hai (jaise `if` block ke andar), jo static analysis ko mushkil banata hai. Practical benefit hai **tree-shaking** — bundlers (Webpack, Vite) unused exports ko final bundle se completely remove kar sakte hain ES Modules ke saath, jo smaller, faster-loading applications banata hai.

## 17. Chapter Summary

ESM aur CommonJS ke key differences hain: sync vs async loading, static vs dynamic imports, browser-native vs bundler-dependent. Node.js `package.json`'s `"type"` field (ya `.mjs`/`.cjs` extensions) se decide karta hai kaunsa system use ho raha hai. Naye projects ES Modules prefer karte hain, but CommonJS existing ecosystem mein pervasive hai.

## 18. Revision Checklist

- [ ] ESM vs CommonJS ka complete comparison table yaad hai.
- [ ] `package.json`'s `"type"` field ka effect clear hai.
- [ ] Module resolution ka basic order samajh gaya.

---

**Module 17 Complete!** Next Module: [18-NodeJS/01-Node-Architecture-and-NPM.md](../18-NodeJS/01-Node-Architecture-and-NPM.md)
