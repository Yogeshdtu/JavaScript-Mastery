# Module 23 — Security

**Difficulty:** 🔴 Advanced
**Chapters:** 4
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 11 (DOM)](../11-DOM-and-Browser/00-Module-Overview.md), [Module 19 (Backend)](../19-Backend-JavaScript/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Ye module optional **nahi** hai. Agar tum koi bhi aisi app banate ho jo users ka data handle karti hai, to security tumhari **responsibility** hai.

Achi baat ye hai: 90% vulnerabilities kuch hi common patterns se aati hain, aur unke fixes simple hain. Ye module wo patterns aur unke fixes sikhata hai.

> 🔒 Ye module interviews mein bhi kaam aata hai — senior roles mein security questions almost guaranteed hain.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [XSS, CSRF, and Injection](./01-XSS-CSRF-and-Injection.md) | ⭐ Teen sabse common attacks aur unke fix |
| 02 | [Prototype Pollution & CORS Mistakes](./02-Prototype-Pollution-and-CORS-Mistakes.md) | JavaScript-specific vulnerabilities |
| 03 | [Auth and Token Security](./03-Auth-and-Token-Security.md) | JWT safely handle karna, cookies, sessions |
| 04 | [Dependency & Data Exposure](./04-Dependency-and-Data-Exposure.md) | npm audit, secrets leak hone se bachana |

---

## Is Module Ke Baad Tum

- [ ] XSS, CSRF, aur SQL injection explain aur **prevent** kar paoge.
- [ ] Tokens ko securely store kar paoge.
- [ ] Samajh jaoge ki authentication ≠ authorization.
- [ ] Apni dependencies ko vulnerabilities ke liye check kar paoge.

---

## The Security Checklist (Yaad Rakhne Layak)

| Attack | Fix |
|--------|-----|
| **XSS** | `textContent` use karo, `innerHTML` nahi (untrusted data ke saath) |
| **SQL Injection** | Parameterized queries — hamesha |
| **CSRF** | Anti-CSRF tokens + `SameSite` cookies |
| **Broken Access Control** | Sirf login check mat karo — **ownership** check karo |
| **Secrets leak** | Environment variables, kabhi frontend code mein nahi |

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **"Authenticated hai" ka matlab "authorized hai" nahi** — user logged in hai, iska matlab ye nahi ki wo **kisi aur ka** order delete kar sakta hai. Har request pe ownership verify karo. Ye sabse common real-world bug hai. (Chapter 03)
- **Frontend validation security nahi hai** — wo sirf UX hai. Attacker seedha API call kar sakta hai. **Server pe hamesha dobara validate karo.** (Chapter 01)
- **`localStorage` mein JWT rakhna XSS ke liye vulnerable hai** — `httpOnly` cookies zyada safe hain. (Chapter 03)
- **API keys frontend mein "chhupa" nahi sakte** — browser mein bheja gaya sab kuch user dekh sakta hai. Secrets sirf server pe. (Chapter 04)

---

## Practice

- **Projects:** [Authentication System](../27-Projects/Advanced/01-Authentication-System/README.md) — ye saare concepts practically use hote hain.
- **Interview prep:** [29-Interview-Preparation/10-Advanced-Questions.md](../29-Interview-Preparation/10-Advanced-Questions.md)

---

**Shuru karo:** [01-XSS-CSRF-and-Injection.md](./01-XSS-CSRF-and-Injection.md)
**Agla Module:** [24-Advanced-JavaScript-Internals](../24-Advanced-JavaScript-Internals/00-Module-Overview.md)
