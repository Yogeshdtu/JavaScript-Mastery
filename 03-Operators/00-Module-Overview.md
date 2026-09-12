# Module 03 — Operators

**Difficulty:** 🟢 Beginner
**Chapters:** 6
**Estimated time:** 4-6 hours
**Prerequisites:** [Module 02](../02-Variables-and-Data-Types/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Operators wo symbols hain jinse tum data pe **kaam** karte ho — jodna, compare karna, check karna. `+`, `-`, `===`, `&&`, `?.` — ye sab operators hain.

Is module ka sabse important chapter hai **Comparison and Equality** (`==` vs `===`). Ye ek chhota sa topic lagta hai, lekin JavaScript ke sabse famous "weird behavior" yahin se aate hain, aur interviews mein ye guaranteed aata hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Arithmetic and Assignment](./01-Arithmetic-and-Assignment-Operators.md) | `+ - * / % **`, `+=`, `++`/`--` ka pre/post behavior |
| 02 | [Comparison and Equality](./02-Comparison-and-Equality.md) | ⚠️ `==` vs ✅ `===` — sabse important chapter |
| 03 | [Logical, Nullish, Optional Chaining](./03-Logical-Nullish-Optional-Chaining.md) | `&&`, `\|\|`, ✅ `??`, ✅ `?.` |
| 04 | [Ternary, Unary, Bitwise](./04-Ternary-Unary-Bitwise.md) | `? :`, unary operators, bitwise basics |
| 05 | [Spread and Rest](./05-Spread-and-Rest.md) | ✅ `...` — copy, merge, collect |
| 06 | [typeof, instanceof, in, delete](./06-typeof-instanceof-in-delete.md) | Type aur property check karne wale operators |

---

## Is Module Ke Baad Tum

- [ ] Hamesha `===` use karoge aur bata paoge `==` kyun risky hai.
- [ ] `??` aur `||` ka farak samajh jaoge (`0` aur `""` ke case mein).
- [ ] `?.` se nested objects safely access kar paoge, crash kiye bina.
- [ ] Spread (`...`) se arrays/objects copy aur merge kar paoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`||` aur `??` same nahi hain** — `0 || "default"` deta hai `"default"` (kyunki `0` falsy hai), lekin `0 ?? "default"` deta hai `0`. Jab `0` ya `""` valid value ho, `??` use karo. (Chapter 03)
- **`==` transitive nahi hai** — `"" == 0` true hai, `0 == "0"` true hai, lekin `"" == "0"` **false** hai. Isliye `===` default hona chahiye. (Chapter 02)
- **Spread shallow copy karta hai** — nested objects ab bhi shared rehte hain. (Chapter 05)
- **`i++` aur `++i`** — value same milti hai jab akela use karo, lekin assignment mein farak padta hai. (Chapter 01)

---

## Practice

- **Coding problems:** [28-Coding-Problems/05-Output-Prediction/](../28-Coding-Problems/05-Output-Prediction/01-Problems.md) — is module ke baad bahut relevant.
- **Cheat sheet:** [30-Revision/Cheat-Sheets/02-Operators.md](../30-Revision/Cheat-Sheets/02-Operators.md)
- **Gotchas:** [30-Revision/JavaScript-Gotchas.md](../30-Revision/JavaScript-Gotchas.md) ke section 1 aur 2.

---

**Shuru karo:** [01-Arithmetic-and-Assignment-Operators.md](./01-Arithmetic-and-Assignment-Operators.md)
**Agla Module:** [04-Control-Flow](../04-Control-Flow/00-Module-Overview.md)
