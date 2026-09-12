# ES Modules

**Module:** 17-Modules
**Difficulty:** 🟡 Intermediate
**Previous:** [16-Fetch-and-APIs/04-Auth-CORS-RateLimiting-Pagination.md](../16-Fetch-and-APIs/04-Auth-CORS-RateLimiting-Pagination.md)
**Next:** [02-CommonJS.md](./02-CommonJS.md)

---

## 1. Learning Objectives

- `import`/`export` syntax (named aur default) use karna.
- ES Modules ka scope-isolation benefit samajhna.
- Dynamic imports ka use-case jaanna.

## 2. Prerequisites

Modules 01-16 complete.

## 3. Concept in Simple Hinglish

**ES Modules (ESM)** JavaScript ka official, modern tareeka hai code ko **multiple files mein organize** karne ka — har file apna khud ka scope rakhti hai (Module 08 chapter 01 ke "global scope pollution" problem ka permanent solution), aur files `export`/`import` ke through ek dusre se cheezein share karti hain.

## 4. Technical Explanation

**Named Exports:** Multiple values ek file se export ho sakte hain, specific names ke saath.

**Default Export:** Ek file sirf **ek** default export rakh sakti hai — import karte waqt koi bhi naam de sakte ho.

**Key characteristics:** ES Modules automatically **strict mode** mein hain (Module 01, chapter 05 se yaad karo), aur har module apna khud ka scope rakhta hai (koi global pollution nahi).

## 5. Syntax

```js
// math.js — named exports
export const PI = 3.14;
export function add(a, b) { return a + b; }

// main.js — importing named exports
import { PI, add } from "./math.js";

// user.js — default export
export default function createUser(name) { return { name }; }

// main.js — importing default export
import createUser from "./user.js"; // Any name works for default imports
```

## 6. Basic Examples

```js
// utils.js
export function greet(name) {
  return `Hello, ${name}`;
}

// app.js
import { greet } from "./utils.js";
console.log(greet("Tanvi")); // "Hello, Tanvi"
```

## 7. Detailed Examples

**Named exports — multiple ways to export:**
```js
// mathUtils.js

// Method 1: Export at declaration
export const PI = 3.14159;
export function square(n) { return n * n; }

// Method 2: Export at the end, grouped together
function cube(n) { return n ** 3; }
function circleArea(r) { return PI * r * r; }
export { cube, circleArea };

// Renaming during export
export { cube as cubeFunction };
```

**Importing — all the variations:**
```js
import { PI, square } from "./mathUtils.js";       // Named imports
import { cube as cubeFn } from "./mathUtils.js";     // Renamed import
import * as MathUtils from "./mathUtils.js";          // Import EVERYTHING as a namespace object
console.log(MathUtils.PI, MathUtils.square(4));
```

**Default export — one per file:**
```js
// Logger.js
export default class Logger {
  log(message) {
    console.log(`[LOG]: ${message}`);
  }
}

// app.js
import Logger from "./Logger.js"; // Name doesn't need to match — it's the DEFAULT
const logger = new Logger();
logger.log("Application started");
```

**Combining default and named exports (common in real projects):**
```js
// api.js
export default function fetchData(url) { /* ... */ }
export const BASE_URL = "https://api.example.com";
export function buildQuery(params) { /* ... */ }

// app.js
import fetchData, { BASE_URL, buildQuery } from "./api.js";
```

**Dynamic imports — loading modules on-demand:**
```js
async function loadHeavyModule() {
  const module = await import("./heavyFeature.js"); // Only loads when actually needed!
  module.default();
}

document.getElementById("loadBtn").addEventListener("click", loadHeavyModule);
```
Dynamic imports (`import()`, ek function ki tarah, Promise return karta hai) **code-splitting/lazy-loading** ke liye use hote hain — poora application-code turant load karne ke bajaye, sirf zaroorat pe specific modules load karna (Module 22 - Performance mein detail).

**Module scope isolation — no global pollution:**
```js
// file1.js
let count = 0; // This 'count' is PRIVATE to file1.js

// file2.js
let count = 100; // A COMPLETELY DIFFERENT 'count' — no conflict, no global pollution!
```

## 8. Mental Model

ES Modules ko socho **alag-alag apartments** ek building mein — har apartment (file) ka apna private space (scope) hai. Agar tumhe kisi doosre apartment ki cheez chahiye, tumhe explicitly "request" karni padti hai (`import`), aur wahi apartment decide karta hai kya "share" (`export`) karna hai — koi bhi cheez automatically shared nahi hoti.

## 9. What Happens Internally?

Browser mein `<script type="module">` ke through ES Modules load hote hain — module dependencies **statically analyzed** hoti hain (`import` statements top-level pe honi chahiye, conditionally nahi — is wajah se tools bundle-optimization kar paate hain). Modules automatically **deferred** load hote hain (jaise `defer` attribute — Module 01 chapter 02 se yaad karo) aur **sirf ek baar** execute hote hain, chahe multiple jagah se import kiye jaayen (module caching).

## 10. Common Mistakes

- Ek file mein multiple `export default` likhne ki koshish karna — sirf ek allowed hai.
- Named aur default import syntax ko confuse karna (curly braces `{}` named ke liye, without braces default ke liye).
- Module files ko file-extension ke bina import karne ki koshish karna jaha extension zaroori ho (environment-dependent — browsers usually require it, bundlers often don't).

## 11. Edge Cases

```html
<script type="module" src="app.js"></script>
<!-- Module scripts are automatically deferred AND automatically in strict mode -->
```
Regular `<script>` tags (bina `type="module"`) `import`/`export` syntax support **nahi** karte — `type="module"` explicitly zaroori hai browsers mein.

## 12. Real-World Usage

Har modern JavaScript project (React, Vue, Node.js with ESM config) ES Modules use karta hai code organize karne ke liye. Dynamic imports large applications mein performance-optimization (lazy-loading routes/features) ke liye standard practice hain.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** ES Modules (`import`/`export`) — official, native browser + modern Node.js support, static analysis-friendly.
**⚠️ Legacy:** CommonJS (`require`/`module.exports`) — Node.js ka original module system (next chapter mein detail).

## 14. Practice Questions

1. Named export aur default export mein kya difference hai (kitne per file)?
2. `import * as Utils from "./utils.js"` kya karta hai?
3. Dynamic import (`import()`) ka use-case kya hai?

## 15. Challenge

3 files banao: `shapes.js` (named exports: `circleArea`, `squareArea`), `Logger.js` (default export: ek class), aur `app.js` jo dono ko import karke use kare.

## 16. Interview Questions

**Q1: ES Modules mein named export aur default export mein kya difference hai?**
A: **Named exports** ek file se **multiple** values export karne dete hain, specific names ke saath — import karte waqt exact same name (curly braces mein) use karna padta hai (ya explicitly rename karna padta hai `as` ke saath). **Default export** ek file se sirf **ek** value export karne deta hai — import karte waqt koi bhi naam use kar sakte ho, kyunki koi specific name expected nahi hai. Ek file dono types combine kar sakti hai (ek default + multiple named), lekin sirf **ek hi default export** allowed hai per file.

**Q2: Dynamic import (`import()`) static `import` statement se kaise different hai?**
A: Static `import` statements file ke **top-level** pe hone chahiye aur module load hote hi **turant** execute hote hain — ye statically analyzable hain (tools ko pata chal jaata hai dependencies kya hain bina code chalaye). Dynamic `import()` ek **function call** hai jo kahi bhi (conditionally, event handlers ke andar) use ho sakta hai, aur ek **Promise return karta hai** jo module ke load hone pe resolve hoti hai. Isका main use-case hai **lazy-loading** — module ko sirf tab load karna jab actually zaroorat ho, initial page-load performance improve karne ke liye.

## 17. Chapter Summary

ES Modules (`import`/`export`) JavaScript ka modern, official module system hai — har file apna scope rakhti hai, explicit `export`/`import` se sharing hoti hai. Named exports multiple values ke liye, default export ek primary value ke liye. Dynamic imports (`import()`) lazy-loading enable karte hain performance-optimization ke liye.

## 18. Revision Checklist

- [ ] Named vs default export/import syntax clear hai.
- [ ] Module scope-isolation ka benefit samajh gaya.
- [ ] Dynamic import ka lazy-loading use-case yaad hai.

---

**Next:** [02-CommonJS.md](./02-CommonJS.md)
