# Caching and Network Optimization

**Module:** 22-Performance
**Difficulty:** 🔴 Advanced
**Previous:** [03-Debounce-Throttle-Lazy-Loading.md](./03-Debounce-Throttle-Lazy-Loading.md)
**Next:** [23-Security/01-XSS-CSRF-and-Injection.md](../23-Security/01-XSS-CSRF-and-Injection.md)

---

## 1. Learning Objectives

- In-memory caching (memoization) se repeated computations avoid karna.
- HTTP caching headers ka basic concept samajhna.
- Code splitting/bundling ke performance-benefits samajhna.

## 2. Prerequisites

Module 16 (Fetch and APIs), Module 17, chapter 01 (Dynamic Imports).

## 3. Concept in Simple Hinglish

**Caching** matlab hai ek baar compute/fetch kiya hua result **store** kar lena, taaki agli baar wahi cheez chahiye ho to dobara compute/fetch na karna pade — turant "cached" result de dena. Ye performance optimization ka sabse powerful, universal technique hai.

## 4. Technical Explanation

**In-memory caching (Memoization):** Function-results ko application-memory mein store karna (Module 08, chapter 04 mein dekha tha).

**HTTP Caching:** Browser/CDN level pe responses ko cache karna, headers (`Cache-Control`, `ETag`) ke through control kiya jaata hai.

**Code Splitting:** Poora JavaScript bundle ek saath load karne ke bajaye, chhote pieces mein todhna, jo on-demand load hon (Module 17, chapter 01 ke dynamic imports).

## 5. Syntax

```js
// Memoization pattern
function memoize(fn) {
  const cache = new Map();
  return function (...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}
```

```
Cache-Control: max-age=3600
ETag: "abc123"
```

## 6. Basic Examples

```js
function memoize(fn) {
  const cache = new Map();
  return function (n) {
    if (cache.has(n)) {
      console.log("From cache!");
      return cache.get(n);
    }
    const result = fn(n);
    cache.set(n, result);
    return result;
  };
}

const slowSquare = (n) => { for (let i = 0; i < 1e8; i++) {} return n * n; }; // Simulated slow function
const fastSquare = memoize(slowSquare);

fastSquare(5); // Slow — computes and caches
fastSquare(5); // Fast — "From cache!"
```

## 7. Detailed Examples

**A generic memoization utility (revisiting Module 08, chapter 04's concepts):**
```js
function memoize(fn) {
  const cache = new Map(); // Module 10, chapter 04!
  return function (...args) {
    const key = JSON.stringify(args); // Simple key generation (works for primitive args)
    if (cache.has(key)) {
      return cache.get(key);
    }
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

function expensiveCalculation(a, b) {
  console.log("Computing...");
  return a ** b; // Imagine this is much more expensive
}

const memoizedCalc = memoize(expensiveCalculation);
console.log(memoizedCalc(2, 10)); // "Computing..." → 1024
console.log(memoizedCalc(2, 10)); // (no "Computing..." log!) → 1024, instantly from cache
```

**Caching Fetch results — avoiding redundant API calls (connects to Module 16):**
```js
const apiCache = new Map();

async function fetchWithCache(url) {
  if (apiCache.has(url)) {
    console.log("Serving from cache:", url);
    return apiCache.get(url);
  }

  const response = await fetch(url);
  const data = await response.json();
  apiCache.set(url, data);
  return data;
}

fetchWithCache("/api/products"); // Actual network request
fetchWithCache("/api/products"); // Instant — served from cache, no network request!
```
**⚠️ Consideration:** Simple caching jaisa upar hai stale-data ka risk rakhta hai (agar server-data change ho jaaye) — real applications mein **cache invalidation strategy** (jaise time-based expiry, ya manual cache-clearing on updates) zaroori hoti hai.

**HTTP Caching headers — browser/server level caching (conceptual, server-side setup):**
```
Response headers from server:
Cache-Control: max-age=3600      → Browser can reuse this response for 1 hour without re-fetching
Cache-Control: no-cache            → Browser MUST revalidate with server before using cached copy
ETag: "v1-abc123"                    → A "fingerprint" of the content, used to check if it changed
```
Jab browser dobara same resource request karta hai, agar `Cache-Control` allow karta hai, browser **network request bina bheje hi** apni local cache se serve kar deta hai — ye especially static assets (images, CSS, JS bundles) ke liye common hai.

**Code splitting — reducing initial bundle size (revisiting Module 17, chapter 01):**
```js
// ⚠️ Without code splitting — everything loads upfront, even rarely-used features
import HeavyChartLibrary from "./HeavyChartLibrary.js";

// ✅ With code splitting (dynamic import) — loads ONLY when needed
async function showChart() {
  const { default: HeavyChartLibrary } = await import("./HeavyChartLibrary.js");
  HeavyChartLibrary.render();
}
document.getElementById("showChartBtn").addEventListener("click", showChart);
```
Agar `HeavyChartLibrary` bahut bada hai aur sirf kuch users hi "Show Chart" button click karte hain, dynamic import ensure karta hai ki **baaki users** ko ye extra code download hi nahi karna padta — unka initial page-load faster rehta hai.

## 8. Mental Model

Caching ko socho **ek chef jo popular dish ka ek portion already-cooked rakhta hai** — agar customer wahi order kare, chef turant serve kar sakta hai (cache hit), fresh cooking (recompute/refetch) ki zaroorat nahi. Code splitting ko socho **ek buffet jaha tum sirf wahi dishes plate mein lete ho jo abhi chahiye** — poora buffet ek saath carry karne ke bajaye (poora bundle load karne ke bajaye), sirf zaroori portions.

## 9. What Happens Internally?

Memoization internally ek `Map`/object use karta hai (Module 10, chapter 04) results ko key-value pairs ki tarah store karne ke liye. HTTP caching browser ke internal cache-storage mein responses ko unke headers ke instructions ke hisaab se store karta hai, aur future requests ke liye pehle cache check karta hai network jaane se pehle. Code splitting build-tools (Webpack, Vite) ke through implement hota hai jo dynamic `import()` calls ko separate JavaScript "chunks" mein compile kar dete hain.

## 10. Common Mistakes

- Memoization use karna functions ke liye jo **side effects** rakhte hain ya frequently-changing external state pe depend karte hain (jaise `Date.now()` involve karne wale functions) — cached result stale/incorrect ho jaayega.
- Cache invalidation strategy na sochna — data change hone pe purana cached data serve hote rehna.
- Har cheez pe caching apply karna jab kuch operations already fast hain — unnecessary complexity add karna.

## 11. Edge Cases

Memoization function-arguments ke `JSON.stringify()`-based keys use karta hai upar wale example mein — ye objects/arrays arguments ke liye kaam karta hai but functions ya circular-references wale arguments ke liye fail ho sakta hai — production memoization libraries (jaise Lodash's `memoize`) zyada robust key-generation use karti hain.

## 12. Real-World Usage

Memoization expensive calculations (data processing, complex UI derivations — React's `useMemo` hook isi concept pe based hai) mein use hota hai. HTTP caching static assets (images, fonts, CSS/JS bundles) ke liye CDNs mein universal hai. Code splitting large-scale applications (jaise multi-page dashboards) mein initial-load-time optimize karne ke liye standard practice hai.

## 13. Comparison With Related Concepts

| Technique | Optimizes |
|-----------|---------------|
| Memoization | Repeated function calls with same arguments |
| HTTP Caching | Repeated network requests for same resource |
| Code Splitting | Initial JavaScript bundle download size |

## 14. Practice Questions

1. Memoization kis type ke functions ke liye appropriate hai (pure vs impure — Module 05, chapter 07 se yaad karo)?
2. `Cache-Control: max-age=3600` ka matlab kya hai?
3. Code splitting initial page-load time ko kaise improve karta hai?

## 15. Challenge

Ek `memoize()` utility function likho (agar upar wala pattern samajh gaya, khud se try karo). Isse ek "slow" Fibonacci function (Module 05, chapter 05 se) pe apply karo aur performance improvement dikhao (`console.time`/`console.timeEnd` use karke, Module 21 chapter 03 se yaad karo).

## 16. Interview Questions

**Q1: Memoization kis type ke functions ke saath safely use kiya ja sakta hai, aur kyun?**
A: Memoization sirf **pure functions** (Module 05, chapter 07 se yaad karo) ke saath safely use karna chahiye — matlab functions jo same input ke liye **hamesha same output** dete hain aur koi external state pe depend nahi karte. Agar function impure hai (jaise external variable ya current-time pe depend karta hai, ya side-effects rakhta hai), cached result **stale ya incorrect** ho sakta hai, kyunki actual computation ka result time-ke-saath ya external-factors ke saath change ho sakta hai, lekin cache purana result hi return karta rahega.

**Q2: Code splitting web-application performance ko kaise improve karta hai?**
A: Bina code splitting ke, poora application ka JavaScript ek **single, large bundle** mein ship hota hai — user ko application use karne se pehle **sab kuch download** karna padta hai, chahe wo abhi kaam ka ho ya na ho. Code splitting (dynamic `import()` ke through, Module 17 chapter 01) application ko **chhote chunks** mein todhta hai jo **on-demand load** hote hain — jaise ek rarely-used feature ka code sirf tab download ho jab user actually us feature ko access kare. Ye **initial page-load time** ko significantly reduce karta hai, kyunki browser ko shuru mein sirf essential code download karna padta hai.

## 17. Chapter Summary

Caching (memoization) repeated computations ko avoid karta hai results store karke — sirf pure functions ke liye appropriate. HTTP caching (headers ke through) repeated network requests avoid karta hai. Code splitting (dynamic imports) initial bundle-size reduce karta hai on-demand loading se. Sab teen techniques milke web-application performance ko significantly improve karte hain.

## 18. Revision Checklist

- [ ] Memoization pattern likh sakta hoon aur uski limitations (pure functions only) samajh gaya.
- [ ] Basic HTTP caching headers ka concept clear hai.
- [ ] Code splitting ka initial-load-time-improvement benefit yaad hai.

---

**Module 22 Complete!** Next Module: [23-Security/01-XSS-CSRF-and-Injection.md](../23-Security/01-XSS-CSRF-and-Injection.md)
