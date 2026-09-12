# npm Scripts, Linting, and Modern Workflow

**Module:** 32-Build-Tools (Bonus Module)
**Difficulty:** 🟡 Intermediate
**Previous:** [02-Bundlers-Webpack-and-Vite.md](./02-Bundlers-Webpack-and-Vite.md)
**Next:** — (Bonus Modules Complete — return to [27-Projects/](../27-Projects/) or continue your main study path)

---

## 1. Learning Objectives

- `package.json` ke `scripts` field se project workflow automate karna.
- ESLint (linting) aur Prettier (formatting) ka purpose aur farak samajhna.
- Ek complete, professional project workflow ka mental picture banana.

## 2. Prerequisites

Module 18 (Node.js, npm basics), [01](./01-Why-a-Build-Step-Transpilation-and-Babel.md) aur [02](./02-Bundlers-Webpack-and-Vite.md) is module ke.

## 3. Concept in Simple Hinglish

Ab tak humne dekha transpilation (Babel), bundling (Webpack/Vite). Ek professional project mein aur bhi kaam automate hote hain — code chalana, test run karna, code-style check karna, build banana — sab ek hi command se. `npm scripts` ye automation provide karte hain. Aur **linting**/**formatting** tools ye ensure karte hain ki poori team ka code consistent style aur quality follow kare, chahe kitne bhi developers kaam kar rahe ho.

## 4. Technical Explanation

**`package.json` ka `scripts` field** — custom commands define karta hai:
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "test": "jest",
    "lint": "eslint src/",
    "format": "prettier --write src/"
  }
}
```
Ye `npm run dev`, `npm run build`, `npm test` (kuch scripts jaise `test`, `start` ke liye `run` optional hai) se chalaye jaate hain — poori team same commands use karti hai, koi manually complex command yaad nahi rakhta.

**ESLint** — code mein potential bugs aur style issues dhundhta hai (static analysis):
```js
// ESLint flag karega:
let x = 5;
// x kabhi reassign nahi hui — 'const' use karni chahiye thi (no-unused-vars, prefer-const rules)

if (user.name = "Admin") { }
// Assignment (=) galti se comparison (===) ki jagah — common typo bug, ESLint isse catch karta hai
```

**Prettier** — code ko automatically ek consistent style mein format karta hai (indentation, quotes, semicolons):
```js
// Before Prettier
const obj={a:1,b:2,c:3}

// After Prettier
const obj = { a: 1, b: 2, c: 3 };
```

**Farak:** ESLint **logic aur potential bugs** pe focus karta hai ("ye code galat ho sakta hai"); Prettier sirf **visual formatting** pe focus karta hai ("ye code consistent dikhna chahiye"). Dono aksar saath use kiye jaate hain.

## 5. Syntax

```bash
npm install -D eslint
npx eslint --init            # config wizard
npx eslint src/                # lint check karo
npx eslint src/ --fix          # auto-fixable issues fix karo

npm install -D prettier
npx prettier --write src/     # poore folder ko format karo
```

## 6. Basic Examples

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint . --ext .js,.jsx",
    "format": "prettier --write ."
  }
}
```
```bash
npm run lint      # code quality issues check karo
npm run format    # code ko auto-format karo
```

## 7. Detailed Examples

**Chaining scripts — ek command, multiple steps:**
```json
{
  "scripts": {
    "prebuild": "npm run lint",
    "build": "vite build",
    "postbuild": "echo 'Build complete!'"
  }
}
```
npm automatically `pre`/`post` prefix wale scripts ko main script se pehle/baad chalata hai — `npm run build` khud-ba-khud pehle lint chalayega, phir build karega, phir completion message dega.

**Git hooks se automation — commit se pehle quality check (Husky jaisi tool ke saath):**
```json
{
  "scripts": {
    "prepare": "husky install"
  },
  "husky": {
    "hooks": {
      "pre-commit": "npm run lint && npm run format"
    }
  }
}
```
Isse team ka **koi bhi member galat-formatted ya lint-failing code commit hi nahi kar sakta** — automation manually reminder dene se zyada reliable hai.

**Complete professional workflow — sab modules ka combination:**
```
1. Developer code likhta hai (VS Code mein ESLint/Prettier extensions real-time feedback dete hain)
2. git commit → pre-commit hook lint + format check karta hai
3. git push → CI/CD pipeline (GitHub Actions) automatically:
   a. npm install
   b. npm run lint
   c. npm test        (Module 21 — Testing)
   d. npm run build    (Babel/TypeScript transpile + Vite/Webpack bundle)
4. Build pass hone pe, automatically deploy (Vercel/Netlify/AWS)
```
Ye poora pipeline is course ke **Module 17 (Modules), 21 (Testing), 26 (Professional), aur 31-32 (TypeScript/Build Tools)** — sab ek saath real project mein kaise combine hote hain, iska complete picture hai.

## 8. Mental Model

npm scripts ko socho **ek restaurant ke standard recipes** ki tarah — "dev" order karo to hamesha wahi steps follow honge, chef (developer) ko har baar poori recipe yaad rakhne ki zarurat nahi. ESLint ko socho ek **quality inspector** jo kaam ke dauraan hi galtiyan pakadta hai; Prettier ko socho ek **uniform-enforcer** jo sabko same style mein present hone ko ensure karta hai, taaki koi bhi kaam kisi ke bhi code ko turant pehchaan sake.

## 9. What Happens Internally?

ESLint code ka AST banata hai (jaise Babel/TypeScript, parsing concept Module 24 se), phir configured "rules" (jaise `no-unused-vars`, `eqeqeq`) us tree pe apply karta hai — har rule tree ke specific patterns dhundhta hai aur violations report karta hai. Prettier bhi AST banata hai, lekin rules check karne ke bajaye, **poore code ko apne opinion ke hisaab se dobara print** karta hai — isliye Prettier "opinionated" hai (bahut kam configuration options), jabki ESLint highly customizable hai.

## 10. Common Mistakes

- ESLint aur Prettier ko ek dusre se conflict karne dena (ESLint ek formatting rule enforce kare, Prettier alag style thopna chahe) — `eslint-config-prettier` jaisi package ye conflicts automatically resolve karti hai.
- Lint errors ko ignore karte rehna ("baad mein fix karenge") — technical debt build hoti rehti hai, aur CI/CD pipeline eventually fail hone lagti hai.
- Sirf local machine pe lint/format chalana, CI mein enforce na karna — isse team members bhool sakte hain aur inconsistent code merge ho jaata hai.

## 11. Edge Cases

- Auto-fix (`eslint --fix`) kuch cheezein automatically fix kar sakta hai (formatting, simple patterns), lekin logic-related warnings (jaise unused variable jo intentionally rakha gaya ho) manual review maangte hain.
- Monorepos (ek repo mein multiple projects) mein scripts aur lint-config ko root level pe share karna common hai — har sub-project alag setup na kare.

## 12. Real-World Usage

- Har professional JavaScript/TypeScript repository mein `.eslintrc`, `.prettierrc`, aur `package.json` scripts milte hain — ye industry-standard baseline hai.
- Open-source projects (React, Vue, etc.) contributors ke liye "run `npm run lint` before submitting a PR" jaisa requirement rakhte hain.
- CI/CD pipelines (GitHub Actions, GitLab CI) `npm run lint`, `npm test`, `npm run build` ko automatically har push/PR pe chalate hain — human review se pehle basic quality-gate.

## 13. Comparison With Related Concepts

| Tool | Checks | Fixes Automatically? | Focus |
|------|--------|------------------------|-------|
| ESLint | Logic issues, potential bugs, style rules | Some rules (`--fix`) | Code correctness + consistency |
| Prettier | Formatting only (spacing, quotes) | Yes, always | Visual consistency |
| TypeScript (`tsc`) | Type errors | No | Type correctness |
| Test runner (Jest) | Behavior/output correctness | No | Functional correctness |

**Together, these four tools cover: types, behavior, logic-quality, and formatting — the four pillars of a professional JS codebase's automated quality gate.**

## 14. Practice Questions

1. ESLint aur Prettier mein core farak kya hai?
2. `pre`/`post` script naming convention kaise kaam karta hai npm mein?
3. CI/CD pipeline mein lint aur test kab chalte hain — pehle ya deploy ke baad?

## 15. Challenge

Apne kisi bhi project (ya is course ke Beginner projects mein se ek) mein `npm install -D eslint prettier`, `npx eslint --init` (basic config), aur ek `.prettierrc` add karo. `package.json` mein `lint` aur `format` scripts likho, aur unhe apne code pe chalao — dekho kya issues report hote hain.

## 16. Interview Questions

**Q1: Ek project mein ESLint, Prettier, aur TypeScript teeno hone ka kya fayda hai — kya inme overlap nahi hai?**
A: Teeno alag layers pe kaam karte hain, overlap minimal hai. TypeScript **type correctness** check karta hai (compile-time) — ki variables/functions sahi type ki values use kar rahe hain. ESLint **code-quality aur potential-bug patterns** check karta hai — jaise unused variables, accidental assignment-instead-of-comparison, ya inconsistent patterns — ye cheezein TypeScript ke type-system ke scope se bahar hain. Prettier sirf **visual formatting** (indentation, quotes, line-length) automate karta hai, taaki team is par manually behas na kare. Teeno mil kar ek comprehensive quality-gate banate hain jo akela koi bhi tool nahi de sakta.

**Q2: CI/CD pipeline mein lint aur test steps build step se pehle kyun rakhe jaate hain?**
A: Ye "fail fast" principle follow karta hai — lint aur unit tests, build/bundle process se **kaafi fast** chalte hain, isliye agar code mein koi basic issue (syntax problem, failing test, lint violation) hai, to pipeline usse jaldi catch karke fail ho jaati hai, bina time-consuming build/bundle step waste kiye. Isse developers ko feedback jaldi milta hai, aur CI resources bhi efficiently use hote hain — ek chhoti si typo ke liye poora expensive build process chalane ki zarurat nahi padti.

## 17. Chapter Summary

`package.json` scripts poore project workflow (dev, build, test, lint, format) ko standardize aur automate karte hain, `pre`/`post` hooks ke saath chain ho sakte hain. ESLint code-quality/bug-patterns check karta hai; Prettier sirf formatting standardize karta hai — dono complementary hain, TypeScript ke saath mil kar ek complete automated quality-gate banate hain jo CI/CD pipelines mein enforce hota hai. Ye is course ke Modules 17, 21, 26, 31, aur 32 — sab ka real-world integration point hai.

## 18. Revision Checklist

- [ ] npm scripts likh aur chain kar sakta hoon (`pre`/`post` hooks samajh gaya).
- [ ] ESLint aur Prettier ka farak clear hai.
- [ ] Complete professional workflow (code → lint/format → test → build → deploy) ka picture bana.
- [ ] Samajh gaya ki TypeScript + ESLint + Prettier + Tests mil kar kaise ek quality-gate banate hain.

---

**🎉 Bonus Modules (31-TypeScript-Bridge aur 32-Build-Tools) Complete!** Ab tum ready ho professional, real-world JavaScript/TypeScript codebases samajhne ke liye. Wapas jaao [27-Projects/](../27-Projects/) apne projects mein ye tools try karne ke liye, ya [00-START-HERE/Progress-Tracker.md](../00-START-HERE/Progress-Tracker.md) update karo.
