# Module 21 — Testing and Debugging

**Difficulty:** 🟠 Advanced
**Chapters:** 3
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 05 (Functions)](../05-Functions/00-Module-Overview.md); backend testing ke liye [Module 19](../19-Backend-JavaScript/00-Module-Overview.md) helpful

---

## Is Module Mein Kya Hai?

Code likhna aadha kaam hai — **ye verify karna ki wo sahi chal raha hai** baaki aadha. Ye module do skills sikhata hai jo tumhe ek "coder" se **professional developer** banate hain:

1. **Testing** — automated code jo tumhare code ko check karta hai.
2. **Debugging** — jab kuch tootta hai, to systematically problem dhoondhna (random changes karke nahi!).

> 💡 Debugging wo skill hai jo tum **har roz** use karoge, chahe tum testing likho ya na likho. Chapter 03 sabse practical hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Testing Fundamentals](./01-Testing-Fundamentals.md) | Unit/integration/E2E, Jest basics, assertions |
| 02 | [Mocking, Stubbing, Spying](./02-Mocking-Stubbing-Spying.md) | External dependencies ko fake karna |
| 03 | [Debugging Tools & Techniques](./03-Debugging-Tools-and-Techniques.md) | ⭐ DevTools, breakpoints, systematic approach |

---

## Is Module Ke Baad Tum

- [ ] Apne functions ke liye unit tests likh paoge.
- [ ] Breakpoints laga ke code ko line-by-line chala paoge.
- [ ] API calls ko mock kar paoge tests mein.
- [ ] Bugs ko **systematically** dhoondh paoge, guess karke nahi.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`console.log` se debugging buri nahi hai** — lekin breakpoints 10x fast hain. Ek baar DevTools ka Sources tab seekh lo, wapas nahi jaoge. (Chapter 03)
- **Mock vs Stub vs Spy** — Stub = fixed answer deta hai; Mock = fixed answer + verify karta hai ki call hua; Spy = real function chalta hai lekin observe hota hai. (Chapter 02)
- **Implementation test mat karo, behavior test karo** — test ye check kare ki function **sahi output** deta hai, ye nahi ki andar kaunsi line chali. Warna har refactor pe tests tootenge. (Chapter 01)
- **100% coverage ka matlab bug-free nahi hai** — coverage batata hai code chala, ye nahi ki sahi chala. (Chapter 01)

---

## Practice

- Apne kisi bhi purane project (jaise [Calculator](../27-Projects/Beginner/01-Calculator/README.md)) ke functions ke liye tests likho — ye sabse accha practice hai.
- **Coding problems:** [28-Coding-Problems/04-Debugging/](../28-Coding-Problems/04-Debugging/01-Problems.md) — ye poora folder is module ke liye hai.

---

**Shuru karo:** [01-Testing-Fundamentals.md](./01-Testing-Fundamentals.md)
**Agla Module:** [22-Performance](../22-Performance/00-Module-Overview.md)
