# Module 08 — Scope and Closures

**Difficulty:** 🟠 Intermediate-Advanced
**Chapters:** 6
**Estimated time:** 8-12 hours
**Prerequisites:** [Module 05 (Functions)](../05-Functions/00-Module-Overview.md) — especially Closures (chapter 06)

---

## Is Module Mein Kya Hai?

Ye module **"JavaScript andar se kaise kaam karti hai"** ka pehla deep dive hai. Yahan tum seekhoge ki engine variables ko dhoondhta kaise hai (scope chain), function call hone pe memory mein kya banta hai (execution context), aur `this` actually decide kaise hota hai.

**Interview reality:** Is module ke 3 topics — closures, `this`, aur execution context — mil ke JavaScript interviews ke sabse bade hisse banate hain. Module 05 ne closures ko introduce kiya tha; yahan hum usko **engine-level** pe samajhenge.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Scope Types](./01-Scope-Types.md) | Global, function, block scope |
| 02 | [Lexical Scope & Scope Chain](./02-Lexical-Scope-and-Scope-Chain.md) | Engine variable dhoondhta kaise hai |
| 03 | [Execution Context & Call Stack](./03-Execution-Context-and-Call-Stack.md) | Function call pe memory mein kya banta hai |
| 04 | [Closures Deep Dive](./04-Closures-Deep-Dive.md) | ⭐ Closures ko execution context ke terms mein samajhna |
| 05 | [**this Keyword**](./05-this-Keyword.md) | ⭐ 4 binding rules aur unki priority |
| 06 | [call, apply, bind](./06-call-apply-bind.md) | `this` ko manually set karna |

---

## Is Module Ke Baad Tum

- [ ] **`this` ke saare 4 rules** aur unki priority bata paoge (new > explicit > implicit > default).
- [ ] Closures ko memory/execution-context ke terms mein explain kar paoge, sirf "inner function" nahi.
- [ ] Call stack trace kar paoge — samajh jaoge error stack traces kaise padhte hain.
- [ ] `call`, `apply`, `bind` ka farak aur use-case jaan jaoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`this` define hone pe decide nahi hota, CALL hone pe decide hota hai** — yahi wo ek line hai jo sab kuch clear kar deti hai. `const fn = obj.greet; fn()` mein `this` kho jaata hai. (Chapter 05)
- **Arrow functions ka apna `this` nahi hota** — wo lexically (surrounding scope se) uthate hain. Isliye `setTimeout` callbacks mein arrow function aksar sahi choice hai, lekin object methods ke liye galat. (Chapter 05)
- **`bind` turant call nahi karta** — wo ek **nayi function** return karta hai. `call`/`apply` turant call karte hain. (Chapter 06)
- **Loop mein `var` ke saath closure** — classic `3, 3, 3` vs `0, 1, 2` problem. `let` har iteration mein naya binding banata hai. (Chapter 04)

---

## Practice

- **Coding problems:** [28-Coding-Problems/05-Output-Prediction/](../28-Coding-Problems/05-Output-Prediction/01-Problems.md) aur [06-Interview-Level](../28-Coding-Problems/06-Interview-Level/01-Problems.md)
- **Cheat sheets:** [06-Scope-and-Closures](../30-Revision/Cheat-Sheets/06-Scope-and-Closures.md), [07-this-Keyword](../30-Revision/Cheat-Sheets/07-this-Keyword.md)
- **Interview prep:** [29-Interview-Preparation/03-Functions-and-Closures.md](../29-Interview-Preparation/03-Functions-and-Closures.md)

---

**Shuru karo:** [01-Scope-Types.md](./01-Scope-Types.md)
**Agla Module:** [09-Type-Coercion](../09-Type-Coercion/00-Module-Overview.md)
