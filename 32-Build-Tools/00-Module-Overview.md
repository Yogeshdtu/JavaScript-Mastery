# Module 32 — Build Tools (Bonus)

**Difficulty:** 🟡🟠 Intermediate to Advanced
**Chapters:** 3
**Estimated time:** 4-6 hours
**Prerequisites:** [Module 17 (Modules)](../17-Modules/00-Module-Overview.md), [Module 18 (npm)](../18-NodeJS/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

> ⚠️ **Ye bhi bonus module hai** — lekin ye ek **real gap** bharta hai.

Is course ke projects mein humne code seedha `<script>` tag se ya `node app.js` se chalaya. Lekin jab tum kisi job mein ya kisi real repository mein jaoge, tumhe milega: `vite.config.js`, `webpack.config.js`, `npm run build`, `.eslintrc`.

**Ye module samjhata hai ki wo sab cheezein kya hain aur kyun hain** — taaki pehli baar dekh ke overwhelm na ho.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Why a Build Step, Transpilation, Babel](./01-Why-a-Build-Step-Transpilation-and-Babel.md) | Build step kyun hota hai, Babel, polyfills |
| 02 | [Bundlers: Webpack and Vite](./02-Bundlers-Webpack-and-Vite.md) | Bundling, code splitting, tree shaking |
| 03 | [npm Scripts, Linting, Modern Workflow](./03-npm-Scripts-Linting-and-Modern-Workflow.md) | ⭐ ESLint, Prettier, complete pro workflow |

---

## Is Module Ke Baad Tum

- [ ] Samajh jaoge `npm run build` actually karta kya hai.
- [ ] Vite se naya project setup kar paoge.
- [ ] ESLint + Prettier configure kar paoge.
- [ ] Kisi bhi professional repo ki config files dekh ke samajh paoge.

---

## The Big Picture

```
Tum likhte ho:        modern JS / TypeScript / JSX, kai files mein
      ↓ Babel / tsc          (transpile — naya syntax → compatible syntax)
      ↓ Vite / Webpack       (bundle — kai files → kam optimized files)
      ↓ Terser               (minify — size chhota karo)
Browser ko milta hai: ek chhoti, fast, compatible file
```

Aur quality ke liye, saath saath:
```
ESLint (bugs/quality) + Prettier (formatting) + Tests + TypeScript (types)
```

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **Transpilation aur Polyfills alag cheezein hain** — Babel **syntax** convert karta hai (arrow function → normal function). Lekin `Promise` ya `fetch` jaise **missing features** ko wo nahi bana sakta — uske liye polyfills (`core-js`) chahiye. (Chapter 01)
- **Tree shaking sirf ESM ke saath reliably chalta hai** — `require()` dynamic hai, isliye bundler confidently nahi bata sakta kya use ho raha hai. Isliye Module 17 important tha. (Chapter 02)
- **ESLint aur Prettier ka kaam alag hai** — ESLint **bugs/quality** dekhta hai, Prettier sirf **formatting**. Dono saath use hote hain. (Chapter 03)
- **Naye projects ke liye Vite choose karo** — Webpack zyadatar purane/enterprise codebases mein milega. (Chapter 02)

---

## Practice

Ek naya Vite project banao (`npm create vite@latest`), usme ESLint + Prettier add karo, aur is course ka koi bhi purana project usme dobara banao. Ye sabse accha practice hai.

---

**Shuru karo:** [01-Why-a-Build-Step-Transpilation-and-Babel.md](./01-Why-a-Build-Step-Transpilation-and-Babel.md)
**Wapas jao:** [27-Projects](../27-Projects/00-Projects-Overview.md) — naye tools ke saath projects try karo
