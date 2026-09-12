# Module 12 — Events

**Difficulty:** 🟡 Intermediate
**Chapters:** 2
**Estimated time:** 3-4 hours
**Prerequisites:** [Module 11 (DOM)](../11-DOM-and-Browser/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Events wo tareeka hain jisse tumhara code **user ki actions pe react** karta hai — click, typing, scroll, submit. DOM ne tumhe page badalne ki power di; events tumhe batate hain **kab** badalna hai.

Ye module chhota hai (sirf 2 chapters) lekin isme ek concept hai — **event delegation** — jo interviews mein regularly poocha jaata hai aur real apps mein performance ke liye important hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Event Basics and Listeners](./01-Event-Basics-and-Listeners.md) | `addEventListener`, event object, listeners hatana |
| 02 | [Bubbling, Capturing, Delegation](./02-Bubbling-Capturing-and-Delegation.md) | ⭐ Events travel kaise karte hain + delegation pattern |

---

## Is Module Ke Baad Tum

- [ ] Kisi bhi user interaction pe react kar paoge.
- [ ] **Event delegation** explain aur implement kar paoge.
- [ ] `event.target` vs `event.currentTarget` ka farak jaan jaoge.
- [ ] Form submit ka default behavior rok paoge (`preventDefault`).

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`event.target` vs `event.currentTarget`** — `target` wo element hai jispe **actually** click hua; `currentTarget` wo element hai **jispe listener laga** hai. Delegation mein yahi farak sab kuch hai. (Chapter 02)
- **Dynamically banaye gaye elements pe listener kaam nahi karta** — kyunki wo element listener lagte waqt exist hi nahi karta tha. Solution: parent pe delegation. (Chapter 02)
- **`removeEventListener` anonymous function ke saath kaam nahi karta** — hatane ke liye wahi function reference chahiye jo lagaya tha. (Chapter 01)
- **Form submit pe page reload ho jaata hai** — `event.preventDefault()` lagana zaroori hai. (Chapter 01)

---

## Practice

- **Projects:** [Quiz App](../27-Projects/Beginner/03-Quiz-App/README.md), [Shopping Cart](../27-Projects/Intermediate/05-Shopping-Cart/README.md) — dono mein delegation practically use hota hai.
- **Cheat sheet:** [30-Revision/Cheat-Sheets/11-DOM-and-Events.md](../30-Revision/Cheat-Sheets/11-DOM-and-Events.md)
- **Interview prep:** [29-Interview-Preparation/06-DOM-and-Browser.md](../29-Interview-Preparation/06-DOM-and-Browser.md)

---

**Shuru karo:** [01-Event-Basics-and-Listeners.md](./01-Event-Basics-and-Listeners.md)
**Agla Module:** [13-Asynchronous-JavaScript](../13-Asynchronous-JavaScript/00-Module-Overview.md)
