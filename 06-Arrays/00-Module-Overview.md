# Module 06 — Arrays

**Difficulty:** 🟡 Intermediate
**Chapters:** 4
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 05](../05-Functions/00-Module-Overview.md) — especially callbacks (chapter 04)

---

## Is Module Mein Kya Hai?

Arrays lists store karte hain — users ki list, products ki list, messages ki list. Real-world code ka bahut bada hissa bas **arrays ko transform karna** hi hota hai.

Is module ka core hai `map`, `filter`, `reduce` — ye teen methods tum **har din** use karoge, har interview mein aayenge, aur har React/Node codebase mein milenge. Inhe callbacks (Module 05) ke bina samajhna mushkil hai, isliye wo module pehle complete karo.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Array Basics](./01-Array-Basics.md) | Banane, access karne, `length`, nested arrays |
| 02 | [Mutating Methods](./02-Mutating-Methods.md) | ⚠️ `push`, `pop`, `splice`, `sort`, `reverse` — original badalte hain |
| 03 | [Non-Mutating Methods](./03-Non-Mutating-Methods.md) | ✅ `map`, `filter`, `reduce`, `slice`, `find` — naya array dete hain |
| 04 | [Iteration & Advanced Methods](./04-Iteration-and-Advanced-Methods.md) | `forEach`, `some`, `every`, `flat`, chaining |

---

## Is Module Ke Baad Tum

- [ ] `map`, `filter`, `reduce` confidently use kar paoge aur unka farak bata paoge.
- [ ] Method chaining likh paoge (`.filter().map().reduce()`).
- [ ] Jaan jaoge kaunsa method original array badalta hai aur kaunsa nahi — ye bug-prevention ke liye critical hai.
- [ ] Array ko sahi tareeke se copy kar paoge (aur samajh jaoge ki wo copy shallow hai).

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`.sort()` numbers ko strings ki tarah sort karta hai!** `[10, 2, 33].sort()` deta hai `[10, 2, 33]`. Numbers ke liye hamesha `.sort((a, b) => a - b)` likho. (Chapter 02) — ye interviews ka favourite question hai.
- **`forEach` kuch return nahi karta** — agar naya array chahiye to `map` use karo. `const x = arr.forEach(...)` hamesha `undefined` dega. (Chapter 04)
- **Mutating vs non-mutating** — `splice` original badalta hai, `slice` nahi. Naam similar, kaam bilkul alag. (Chapter 02 vs 03)
- **`reduce` sabse intimidating lagta hai** — bas socho: "ek array ko ek single value mein nichodna". Accumulator + current value, bas. (Chapter 03)

---

## Practice

- **Coding problems:** [28-Coding-Problems/02-Intermediate/](../28-Coding-Problems/02-Intermediate/01-Problems.md) — array problems yahan bahut hain.
- **Projects:** [Quiz App](../27-Projects/Beginner/03-Quiz-App/README.md), [To-Do App](../27-Projects/Beginner/04-To-Do-App/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/03-Arrays.md](../30-Revision/Cheat-Sheets/03-Arrays.md)

---

**Shuru karo:** [01-Array-Basics.md](./01-Array-Basics.md)
**Agla Module:** [07-Objects](../07-Objects/00-Module-Overview.md)
