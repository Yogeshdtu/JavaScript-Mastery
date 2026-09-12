# Module 04 — Control Flow

**Difficulty:** 🟢 Beginner
**Chapters:** 4
**Estimated time:** 3-5 hours
**Prerequisites:** [Module 03](../03-Operators/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Ab tak tumhara code upar se neeche, ek seedhi line mein chalta tha. Control flow se tum code ko **decisions** lene do ge (`if/else`) aur **kaam repeat** karwaoge (loops).

Ye module chhota hai lekin **bahut practical** — iske baad tum pehli baar actual working programs bana paoge. Module 05 (Functions) ke saath mil ke, ye tumhe pehla real project (Calculator, Number Guessing Game) banane layak bana dega.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [If-Else-Switch](./01-If-Else-Switch.md) | Conditions, `else if` chains, `switch` kab use karein |
| 02 | [Loops (for, while, do-while)](./02-Loops-for-while-dowhile.md) | Repetition — kaunsa loop kab |
| 03 | [for...of and for...in](./03-for-of-and-for-in.md) | ✅ `for...of` (values) vs `for...in` (keys) |
| 04 | [break and continue](./04-break-and-continue.md) | Loop ko beech mein rokna ya skip karna |

---

## Is Module Ke Baad Tum

- [ ] Conditions ke basis pe alag-alag code chala paoge.
- [ ] Arrays aur objects pe loop laga paoge.
- [ ] Sahi loop choose kar paoge (`for` vs `for...of` vs `while`).
- [ ] Infinite loop se bach paoge (aur agar phas jao to Ctrl+C dabana yaad rahega!).

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`for...in` arrays ke liye nahi hai** — wo **keys/indexes** deta hai (strings mein!), values nahi. Arrays ke liye ✅ `for...of` ya `.forEach()` use karo. (Chapter 03)
- **`switch` mein `break` bhoolna** — bina `break` ke, execution agle case mein "fall through" ho jaata hai. Ye kabhi-kabhi intentional hota hai, lekin 95% baar bug hota hai. (Chapter 01)
- **Loop condition kabhi false nahi hoti** → infinite loop, browser freeze. Counter update karna mat bhoolo. (Chapter 02)

---

## Practice

- **Coding problems:** [28-Coding-Problems/01-Beginner/](../28-Coding-Problems/01-Beginner/01-Problems.md) — ab tum zyadatar beginner problems kar sakte ho.
- **Project (Module 05 ke baad):** [Calculator](../27-Projects/Beginner/01-Calculator/README.md), [Number Guessing Game](../27-Projects/Beginner/02-Number-Guessing-Game/README.md)

---

**Shuru karo:** [01-If-Else-Switch.md](./01-If-Else-Switch.md)
**Agla Module:** [05-Functions](../05-Functions/00-Module-Overview.md)
