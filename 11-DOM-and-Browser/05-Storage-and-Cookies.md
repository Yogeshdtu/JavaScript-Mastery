# localStorage, sessionStorage, and Cookies

**Module:** 11-DOM-and-Browser
**Difficulty:** 🟡 Intermediate
**Previous:** [04-Forms-and-Validation.md](./04-Forms-and-Validation.md)
**Next:** [06-Browser-APIs.md](./06-Browser-APIs.md)

---

## 1. Learning Objectives

- `localStorage` aur `sessionStorage` ka use samajhna.
- Cookies ka basic mechanism aur use-case samajhna.
- Teeno storage options ke beech sahi choice karna.

## 2. Prerequisites

Module 11, chapters 01-04.

## 3. Concept in Simple Hinglish

Browser ke paas kuch tareeke hain **data ko save** karne ke, page reload/close hone ke baad bhi. `localStorage` data ko **permanently** (jab tak manually clear na ho) save karta hai. `sessionStorage` sirf tab tak jab tak **tab open** hai. `Cookies` purana tareeka hai jo **server ko bhi automatically bhejta hai** har request ke saath.

## 4. Technical Explanation

| Storage | Persists Until | Sent to Server? | Size Limit | Accessible From |
|---------|-------------------|---------------------|---------------|----------------------|
| `localStorage` | Manually cleared | No | ~5-10MB | Same origin, any tab |
| `sessionStorage` | Tab closed | No | ~5-10MB | Same origin, same tab only |
| Cookies | Expiry date set | ✅ Yes, every HTTP request | ~4KB | Same origin (configurable) |

Sabhi ye storages **string-only** hote hain — objects ko store karne ke liye `JSON.stringify()`/`JSON.parse()` use karna padta hai.

## 5. Syntax

```js
localStorage.setItem("key", "value");
localStorage.getItem("key");
localStorage.removeItem("key");
localStorage.clear();

sessionStorage.setItem("key", "value"); // Same API as localStorage

document.cookie = "key=value; expires=...; path=/";
```

## 6. Basic Examples

```js
localStorage.setItem("username", "Aditi");
console.log(localStorage.getItem("username")); // "Aditi"
localStorage.removeItem("username");
console.log(localStorage.getItem("username")); // null
```

## 7. Detailed Examples

**Storing objects — the JSON conversion pattern:**
```js
const user = { name: "Rohan", theme: "dark", loginCount: 5 };

localStorage.setItem("user", JSON.stringify(user)); // Must stringify — storage only holds strings!

const retrieved = JSON.parse(localStorage.getItem("user"));
console.log(retrieved.name); // "Rohan"
console.log(typeof retrieved); // "object" — successfully parsed back
```
**⚠️ Common mistake:** `localStorage.setItem("user", user)` (bina stringify ke) — ye object ko `"[object Object]"` string mein convert kar dega, data effectively lost ho jaata hai.

**`localStorage` vs `sessionStorage` — practical difference:**
```js
localStorage.setItem("persistentData", "I survive tab close and browser restart");
sessionStorage.setItem("temporaryData", "I disappear when this tab closes");

// Close and reopen the tab:
console.log(localStorage.getItem("persistentData"));  // Still there!
console.log(sessionStorage.getItem("temporaryData"));  // null — gone!
```

**A practical use-case — remembering theme preference:**
```js
function applyTheme() {
  const savedTheme = localStorage.getItem("theme") || "light";
  document.body.classList.toggle("dark-theme", savedTheme === "dark");
}

function toggleTheme() {
  const isDark = document.body.classList.toggle("dark-theme");
  localStorage.setItem("theme", isDark ? "dark" : "light");
}

applyTheme(); // Run on page load to restore saved preference
```

**Cookies — basic manipulation (verbose, legacy-feeling API):**
```js
document.cookie = "username=Kabir; max-age=3600; path=/"; // Expires in 1 hour

console.log(document.cookie); // "username=Kabir" — a single string of ALL cookies!

// Reading a specific cookie requires manual parsing:
function getCookie(name) {
  const cookies = document.cookie.split("; ");
  const found = cookies.find(row => row.startsWith(name + "="));
  return found ? found.split("=")[1] : null;
}
console.log(getCookie("username")); // "Kabir"
```
Cookies ka API **verbose aur manual-parsing-heavy** hai — isi liye jab tak server ko data bhejna zaroori na ho (authentication tokens, session IDs), `localStorage`/`sessionStorage` prefer kiye jaate hain client-side data ke liye.

## 8. Mental Model

`localStorage` ko socho ek **permanent locker** — jab tak khud na kholo (clear na karo), saman andar rehta hai. `sessionStorage` ek **temporary locker jo sirf ek visit (tab session) tak available hai** — visit khatam, locker khali. Cookies ko socho ek **ID card jo tum har office visit (HTTP request) pe automatically saath le jaate ho** — server bhi ise dekh sakta hai.

## 9. What Happens Internally?

`localStorage`/`sessionStorage` browser ke andar ek key-value store hain, per-origin (domain) isolated — koi dusri website tumhara data access nahi kar sakti. Cookies har HTTP request ke `Cookie` header mein automatically attach ho jaate hain — isi liye server-side authentication (session cookies) mein use hote hain, lekin isi wajah se ye request size bhi badhate hain (Module 16 mein HTTP headers detail).

## 10. Common Mistakes

- Objects ko `JSON.stringify()` kiye bina store karna.
- Sensitive data (passwords, raw tokens) ko `localStorage` mein store karna — XSS attacks se vulnerable hai (Module 23 mein detail — httpOnly cookies zyada secure hain sensitive tokens ke liye).
- `sessionStorage` ko `localStorage` jaisa persistent expect karna.

## 11. Edge Cases

```js
try {
  localStorage.setItem("test", "x".repeat(10000000)); // Very large data
} catch (e) {
  console.log(e.name); // "QuotaExceededError" — storage limits exist!
}
```

## 12. Real-World Usage

`localStorage` theme preferences, cart items (guest checkout), form-draft-auto-save mein use hota hai. `sessionStorage` multi-step-form data (jo submit hone tak temporary honi chahiye) mein. Cookies authentication sessions mein (especially `httpOnly` cookies, jo JavaScript se access hi nahi ho sakte — extra security).

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** `localStorage`/`sessionStorage` client-side-only data ke liye (preferences, drafts).
**⚠️ Necessary but verbose:** Cookies jab server ko automatically data chahiye ho (authentication) — `httpOnly` cookies security-sensitive data ke liye best practice hain.

## 14. Practice Questions

1. `localStorage` aur `sessionStorage` mein persistence ka difference kya hai?
2. Objects ko storage mein save karne se pehle kya karna zaroori hai?
3. Cookies server ko automatically kyun bhejte hain, jabki `localStorage` nahi?

## 15. Challenge

Ek simple "recently viewed items" feature banao — `localStorage` use karke ek array of item-names store karo, naya item add karte waqt duplicates avoid karo aur max 5 items rakho.

## 16. Interview Questions

**Q1: `localStorage`, `sessionStorage`, aur cookies mein kab kaunsa use karna chahiye?**
A: `localStorage` tab use karo jab data **permanently** (jab tak explicitly clear na ho) client-side save karna ho aur server ko bhejne ki zaroorat na ho — jaise theme preference. `sessionStorage` tab jab data sirf **current tab session** tak chahiye — jaise multi-step form ka temporary progress. **Cookies** tab zaroori hain jab data **server ko automatically har request ke saath** bhejna ho — jaise authentication session tokens, especially `httpOnly` cookies ke saath jo JavaScript se accessible nahi hote (extra XSS protection).

**Q2: `localStorage` mein object store karne se pehle `JSON.stringify()` kyun zaroori hai?**
A: `localStorage` (aur `sessionStorage`) sirf **strings** store kar sakte hain. Agar tum directly ek object pass karo `setItem()` ko, JavaScript usse automatically `.toString()` call karke convert kar deta hai, jo objects ke liye `"[object Object]"` deta hai — actual data lost ho jaata hai. `JSON.stringify()` object ko ek accurate, parseable string representation mein convert karta hai, jisse baad mein `JSON.parse()` se original object structure wapas mil sake.

## 17. Chapter Summary

`localStorage` permanent client-side storage deta hai, `sessionStorage` sirf tab-session tak. Dono sirf strings store karte hain — objects ke liye `JSON.stringify`/`parse` chahiye. Cookies server ko automatically bhejte hain har request mein — authentication ke liye zaroori, lekin general client-data ke liye `localStorage`/`sessionStorage` better hain.

## 18. Revision Checklist

- [ ] Teeno storage options ka persistence/scope difference yaad hai.
- [ ] Objects store karne ka JSON pattern samajh gaya.
- [ ] Cookies ka server-communication behavior clear hai.

---

**Next:** [06-Browser-APIs.md](./06-Browser-APIs.md)
