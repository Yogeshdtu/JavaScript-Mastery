# Module 31 — TypeScript Bridge (Bonus)

**Difficulty:** 🟡🟠 Intermediate to Advanced
**Chapters:** 4
**Estimated time:** 6-8 hours
**Prerequisites:** Modules 01-10 solid; ideally [Module 19 (Backend)](../19-Backend-JavaScript/00-Module-Overview.md) tak

---

## Is Module Mein Kya Hai?

> ⚠️ **Ye bonus module hai** — course ke mandatory spine (01-30) ka hissa nahi. Lekin **job market ke liye ye lagbhag zaroori hai.**

Aaj ke zyadatar JavaScript jobs mein "JavaScript/TypeScript" ek saath maanga jaata hai. TypeScript koi nayi language nahi hai — ye JavaScript hi hai **plus types**. Tumhara saara JS knowledge waise ka waisa kaam aayega.

**Ye module kya karta hai:** Tumhe 6-8 ghante mein "TypeScript code padh aur likh sakta hoon" level pe le jaata hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Why TypeScript and Setup](./01-Why-TypeScript-and-Setup.md) | TS kya hai, `tsc`, type erasure, project setup |
| 02 | [Basic Types and Interfaces](./02-Basic-Types-and-Interfaces.md) | Annotations, `interface`, `any` vs `unknown` |
| 03 | [Functions, Generics, Utility Types](./03-Functions-Generics-and-Utility-Types.md) | `<T>`, `Partial`, `Pick`, `Omit`, `Record` |
| 04 | [Migrating JS to TS](./04-Migrating-JS-to-TS-and-Real-World-Usage.md) | Incremental migration, `@types/*`, React/Node examples |

---

## Is Module Ke Baad Tum

- [ ] TypeScript code **padh** paoge (jo job mein pehla din chahiye hota hai).
- [ ] Basic types aur interfaces likh paoge.
- [ ] Generics samajh jaoge aur simple generic functions likh paoge.
- [ ] Ek existing JS project ko gradually TS mein convert kar paoge.

---

## Kab Padhna Hai

Module 19 ke baad kabhi bhi. Best time: **[27-Projects](../27-Projects/00-Projects-Overview.md) se pehle ya uske parallel** — taaki tum apne projects mein TypeScript try kar sako.

Iske saath [32-Build-Tools](../32-Build-Tools/00-Module-Overview.md) padhna best rahega — dono mil ke "professional project kaisa dikhta hai" ka poora picture dete hain.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **TypeScript "nayi language" nahi hai** — har valid JavaScript, valid TypeScript hai. Tum aaj hi `.js` ko `.ts` rename kar sakte ho aur wo chalega. (Chapter 01)
- **Types runtime pe exist nahi karte** — compile ke baad sab erase ho jaate hain. Isliye TS API se aaye **galat data** ko nahi pakad sakta — uske liye runtime validation (jaise `zod`) chahiye. (Chapter 01)
- **`any` use karke errors "fix" karna** — isse TypeScript ka poora fayda khatam ho jaata hai. `unknown` use karo. (Chapter 02)
- **`@types/lodash` kyun chahiye jab `lodash` install hai** — kyunki purani libraries mein type information hoti hi nahi. (Chapter 04)

---

**Shuru karo:** [01-Why-TypeScript-and-Setup.md](./01-Why-TypeScript-and-Setup.md)
**Agla Module:** [32-Build-Tools](../32-Build-Tools/00-Module-Overview.md)
