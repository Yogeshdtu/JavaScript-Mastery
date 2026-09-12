# Prototype Pollution and CORS Mistakes

**Module:** 23-Security
**Difficulty:** 🔴 Advanced
**Previous:** [01-XSS-CSRF-and-Injection.md](./01-XSS-CSRF-and-Injection.md)
**Next:** [03-Auth-and-Token-Security.md](./03-Auth-and-Token-Security.md)

---

## 1. Learning Objectives

- Prototype Pollution attack ka mechanism samajhna (Module 10, chapter 03 se aage badhate hue).
- Common CORS misconfigurations identify karna (Module 16, chapter 04 se aage badhate hue).
- In JavaScript-specific vulnerabilities se defend karna seekhna.

## 2. Prerequisites

Module 10, chapter 03 (Prototypes), Module 16, chapter 04 (CORS).

## 3. Concept in Simple Hinglish

**Prototype Pollution** ek JavaScript-specific vulnerability hai jisme attacker `Object.prototype` (jo **saare** objects ke liye shared hai) ko modify kar deta hai, jisse poori application ka behavior unexpected tarike se change ho sakta hai. **CORS misconfigurations** tab hoti hain jab servers CORS ko galat tarike se setup karte hain, security ko unintentionally weaken karte hue.

## 4. Technical Explanation

**Prototype Pollution:** Jab untrusted input ko object mein merge/assign kiya jaata hai bina proper checks ke, aur us input mein `__proto__` ya `constructor.prototype` jaisi special keys hoti hain, attacker **`Object.prototype`** ko directly modify kar sakta hai — jo application ke **saare objects** ko affect karta hai (Module 10, chapter 03 ke prototype-chain concept ko yaad karo).

**CORS Misconfigurations:** Common mistakes jaise `Access-Control-Allow-Origin: *` ko sensitive, credential-requiring endpoints ke saath use karna.

## 5. Syntax

```js
// ⚠️ Vulnerable merge function
function merge(target, source) {
  for (const key in source) {
    target[key] = source[key];
  }
  return target;
}
```

## 6. Basic Examples

```js
// Demonstrating how Object.prototype pollution works (educational)
const obj = {};
console.log(obj.isAdmin); // undefined

Object.prototype.isAdmin = true; // Polluting the shared prototype!

console.log(obj.isAdmin); // true — EVERY object now has this property!
delete Object.prototype.isAdmin; // Cleanup for the example
```

## 7. Detailed Examples

**Prototype Pollution attack — how it actually happens:**
```js
// ⚠️ A vulnerable "merge" function (common in configuration-merging utilities)
function unsafeMerge(target, source) {
  for (const key in source) {
    if (typeof source[key] === "object" && source[key] !== null) {
      if (!target[key]) target[key] = {};
      unsafeMerge(target[key], source[key]); // Recursive merge
    } else {
      target[key] = source[key];
    }
  }
  return target;
}

// If 'source' comes from untrusted user input (e.g., a JSON request body):
const maliciousInput = JSON.parse('{"__proto__": {"isAdmin": true}}');

const userConfig = {};
unsafeMerge(userConfig, maliciousInput);

// Now check ANY object in the application:
const innocentObject = {};
console.log(innocentObject.isAdmin); // true! ⚠️ Every object is now "polluted"!

// If the application has logic like: if (user.isAdmin) { grantAdminAccess(); }
// This could allow a privilege escalation attack across the ENTIRE application!
```

**✅ Prevention — safe merging practices:**
```js
function safeMerge(target, source) {
  for (const key in source) {
    if (key === "__proto__" || key === "constructor" || key === "prototype") {
      continue; // Explicitly skip dangerous keys!
    }
    if (typeof source[key] === "object" && source[key] !== null) {
      if (!target[key]) target[key] = {};
      safeMerge(target[key], source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}

// ✅ Even better — use Object.create(null) for objects that don't need a prototype at all
const safeObject = Object.create(null); // Has NO prototype — immune to this specific attack vector
```
**Note:** Modern versions of popular libraries (Lodash, etc.) have patched known prototype-pollution vulnerabilities — but understanding the underlying risk is important when writing your own merge/clone utilities, or when using older library versions.

**CORS misconfiguration — the dangerous combination:**
```
⚠️ DANGEROUS combination:
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

// This combination is actually INVALID/blocked by browsers when credentials are involved —
// but developers sometimes try to work around it by dynamically reflecting the request's
// Origin header back, which effectively achieves the same dangerous result:

Access-Control-Allow-Origin: <whatever origin sent the request>  ⚠️ Reflects ANY origin!
Access-Control-Allow-Credentials: true

// This means ANY website can make authenticated (cookie-included) requests to your API
// on behalf of a logged-in user — a serious security hole.
```
**✅ Correct approach — explicit allow-list:**
```js
const allowedOrigins = ["https://myapp.com", "https://admin.myapp.com"];

app.use((req, res, next) => {
  const origin = req.headers.origin;
  if (allowedOrigins.includes(origin)) {
    res.setHeader("Access-Control-Allow-Origin", origin); // Only reflect KNOWN, trusted origins
    res.setHeader("Access-Control-Allow-Credentials", "true");
  }
  next();
});
```

## 8. Mental Model

Prototype Pollution ko socho **ek shared "master blueprint"** (Object.prototype) ko modify karna jisse **har naya ghar** (object) jo abhi bhi ban raha hai (ya already bana hai) uss modification ko inherit kar leta hai — ek single attack se poore "neighborhood" (application) ko affect kiya ja sakta hai. CORS misconfiguration ko socho ek **security guard jo galti se "sabko andar aane do" bol deta hai**, jab actually sirf specific, verified visitors (trusted origins) ko allow karna chahiye tha.

## 9. What Happens Internally?

Jab `obj.__proto__.someProperty = value` (ya equivalent merge-based manipulation) execute hota hai, ye directly `Object.prototype` (jo **globally shared** hai sabhi plain objects ke through prototype-chain, Module 10 chapter 03 se yaad karo) ko modify kar deta hai — koi bhi object jo abhi tak nahi bana, ya already bana hua hai (agar wo property directly set na ho), is polluted property ko apni prototype-chain lookup ke through "inherit" kar lega.

## 10. Common Mistakes

- User-input ko deep-merge functions mein bina key-filtering ke pass karna.
- CORS ko debug karte waqt "quick fix" ki tarah `Access-Control-Allow-Origin: *` use karna production credential-requiring endpoints pe.
- Purani library versions use karna jinme known prototype-pollution vulnerabilities patched nahi hui.

## 11. Edge Cases

`Object.freeze(Object.prototype)` ek defensive measure ho sakta hai (poori application ke liye `Object.prototype` ko immutable banana) — lekin ye kuch legitimate library-behaviors ko bhi break kar sakta hai jo prototype ko extend karti hain, isliye caution ke saath use karna chahiye.

## 12. Real-World Usage

Prototype Pollution real-world vulnerabilities kai popular npm packages mein discover hue hain (jaise older versions of Lodash) — ye dikhata hai ki even well-established libraries bhi is risk se immune nahi hain, aur dependencies ko updated rakhna (Module 18, chapter 01 ke `package-lock.json` concept se related) security ke liye important hai.

## 13. Comparison With Related Concepts

| Vulnerability | Root Cause | Prevention |
|-----------------|----------------|----------------|
| Prototype Pollution | Unfiltered merge of untrusted objects | Key-filtering, `Object.create(null)` |
| CORS Misconfiguration | Overly permissive origin-allowance | Explicit origin allow-lists |

## 14. Practice Questions

1. Prototype Pollution attack `Object.prototype` ko specifically kyun target karta hai?
2. `Access-Control-Allow-Origin: *` ke saath credentials use karna kyun dangerous hai?
3. Safe merge function mein kaunsi keys explicitly skip karni chahiye?

## 15. Challenge

Upar diye gaye `unsafeMerge` function ko lo aur usse `safeMerge` mein convert karo (dangerous keys skip karke), phir test karo ki malicious `__proto__` input ab `Object.prototype` ko pollute nahi karta.

## 16. Interview Questions

**Q1: Prototype Pollution attack kya hai aur ye application ke liye kyun dangerous hai?**
A: Prototype Pollution tab hoti hai jab attacker, ek insufficiently-validated merge/clone function ke through, `Object.prototype` (jo JavaScript ke **saare plain objects** ke liye shared hai prototype-chain ke through — Module 10, chapter 03) ko modify kar deta hai, keys jaise `__proto__` ka use karke. Ye dangerous hai kyunki ek single successful attack **poori application ke har object** ko affect kar sakta hai — jaise agar attacker `isAdmin: true` ko globally inject kar de, application ke saare authorization-checks (jo `user.isAdmin` check karte hain) compromise ho sakte hain, chahe wo specific object directly attacker se related na ho.

**Q2: `Access-Control-Allow-Origin` header ko dynamically request ke `Origin` se reflect karna (bina validation ke) kyun risky hai?**
A: Agar server blindly kisi bhi incoming request ke `Origin` header ko `Access-Control-Allow-Origin` response mein reflect kar deta hai (especially `Access-Control-Allow-Credentials: true` ke saath), ye effectively **kisi bhi website** ko allow kar deta hai user ke credentials (cookies) ke saath authenticated requests bhejne ki — chahe wo website completely unrelated/malicious ho. Ye CORS ke poore purpose (specific, trusted origins ko hi cross-origin access dena) ko defeat kar deta hai. Sahi approach hai explicit **allow-list** maintain karna trusted origins ki, aur sirf unhi ko reflect karna.

## 17. Chapter Summary

Prototype Pollution `Object.prototype` ko modify karke poori application ko affect kar sakti hai — merge functions mein dangerous keys (`__proto__`, `constructor`, `prototype`) ko filter karke prevent hoti hai. CORS misconfigurations (jaise unrestricted origin-reflection with credentials) unintentionally sensitive endpoints ko expose kar sakti hain — explicit origin allow-lists sahi approach hai.

## 18. Revision Checklist

- [ ] Prototype Pollution ka mechanism (Object.prototype ka global-impact) clear hai.
- [ ] Safe-merge pattern (dangerous-key filtering) likh sakta hoon.
- [ ] CORS ke saath credentials ka dangerous-combination samajh gaya.

---

**Next:** [03-Auth-and-Token-Security.md](./03-Auth-and-Token-Security.md)
