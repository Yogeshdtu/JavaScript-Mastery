# How JavaScript Runs

**Module:** 01-JavaScript-Foundations
**Difficulty:** 🟢 Beginner
**Previous:** [02-Environment-Setup.md](./02-Environment-Setup.md)
**Next:** [04-Syntax-and-Statements.md](./04-Syntax-and-Statements.md)

---

## 1. Learning Objectives

- Browser vs Node.js runtime environment ka difference samajhna.
- JavaScript engine ke basic working ka idea paana (parsing, compilation, execution).
- "Runtime environment" ka concept clear karna.

## 2. Prerequisites

[01-What-is-JavaScript.md](./01-What-is-JavaScript.md), [02-Environment-Setup.md](./02-Environment-Setup.md)

## 3. Concept in Simple Hinglish

JavaScript khud se kuch nahi kar sakti — usse ek "ghar" (environment) chahiye jaha wo chal sake. Browser ek environment hai (jaha DOM, `window`, `document` milte hain), Node.js dusra environment hai (jaha file system, `require`, servers milte hain). Same JavaScript language, do different "ghar" mein rehke different superpowers paati hai.

## 4. Technical Explanation

**Runtime Environment** = JavaScript Engine + extra APIs jo environment provide karta hai.

**Browser Runtime:**
- Engine: V8 (Chrome/Edge), SpiderMonkey (Firefox)
- Extra APIs: `window`, `document`, `fetch`, `localStorage`, DOM APIs

**Node.js Runtime:**
- Engine: V8 (Node bhi V8 use karta hai!)
- Extra APIs: `fs` (file system), `process`, `require`/`module`, `http`

Dono runtimes same **ECMAScript core** (variables, functions, arrays, objects, etc.) support karte hain, lekin extra APIs completely different hote hain. Isi liye browser ka `document.querySelector` Node.js mein kaam nahi karega, aur Node.js ka `fs.readFile` browser mein nahi chalega.

## 5. Syntax

```js
// Browser-only
console.log(window.innerWidth);

// Node-only
const fs = require("fs");
console.log(process.version);

// Both environments mein chalega (core JS)
console.log([1, 2, 3].map(x => x * 2));
```

## 6. Basic Examples

```js
console.log(typeof window);   // Browser: "object" | Node: "undefined"
console.log(typeof process);  // Browser: "undefined" | Node: "object"
```

## 7. Detailed Examples

```js
function detectEnvironment() {
  if (typeof window !== "undefined") {
    return "Browser";
  } else if (typeof process !== "undefined") {
    return "Node.js";
  }
  return "Unknown";
}

console.log(detectEnvironment());
```

Ye function `typeof` use karke check karta hai kaunsa global object available hai — `window` (browser) ya `process` (Node.js). Isi tarah ke checks real libraries (jaise Axios) mein use hote hain jo dono environments support karti hain (isomorphic/universal JavaScript).

## 8. Mental Model

JavaScript engine ko socho ek **car ka engine** ki tarah — same engine (V8) different "cars" (Chrome, Node.js, Edge) mein fit ho sakta hai, lekin har car ke apne extra features (AC, music system) hote hain jo engine khud provide nahi karta — environment provide karta hai.

## 9. What Happens Internally?

Engine ke andar 3 main steps hote hain (high level):

1. **Parsing:** Code text ko tokens mein todha jaata hai, phir AST (Abstract Syntax Tree) banta hai.
2. **Compilation:** Modern engines (V8) AST ko turant bytecode mein compile karte hain (Ignition interpreter), aur "hot" (frequently-run) code ko optimized machine code mein convert karte hain (TurboFan JIT compiler).
3. **Execution:** Call stack pe code execute hota hai, memory heap mein objects store hote hain.

Ye process Module 24 (Advanced Internals) mein bahut deeply cover hoga.

## 10. Common Mistakes

- Browser ke liye likha code (jaise `document.getElementById`) seedha Node.js mein chalane ki koshish karna — error aayega kyunki `document` Node.js mein exist nahi karta.
- "JavaScript engine aur runtime environment same cheez hai" sochna — Engine sirf core language chalata hai, runtime environment usme extra APIs add karta hai.

## 11. Edge Cases

- Deno aur Bun jaise naye JavaScript runtimes bhi hain (Node.js ke alternatives) jo apne extra APIs provide karte hain, but core ECMAScript same rehta hai.
- Kuch code "isomorphic" hota hai — matlab dono environments mein bina modification chal jaata hai, jab tak wo sirf core JS features use kare.

## 12. Real-World Usage

Frameworks jaise Next.js dono runtime mein code chalate hain (server-side Node.js pe, client-side browser pe) — isi liye developers ko pata hona chahiye kaunsa code kaha chalega.

## 13. Comparison With Related Concepts

| Aspect | Browser | Node.js |
|--------|---------|---------|
| Engine | V8 (Chrome/Edge) | V8 |
| Global object | `window` | `global` |
| DOM access | Yes | No |
| File system access | No | Yes (`fs`) |
| Module system default | ES Modules | CommonJS (historically) |

## 14. Practice Questions

1. Browser aur Node.js mein kaunsa engine common hai?
2. `typeof window` Node.js mein kya return karega?
3. "Isomorphic JavaScript" ka matlab kya hai?

## 15. Challenge

Ek chhota function likho jo detect kare ki code Browser ya Node.js mein chal raha hai, aur accordingly different message log kare — dono environments mein test karo (agar Node.js abhi install nahi kiya, to sirf browser mein test karo aur expected Node.js output predict karo).

## 16. Interview Questions

**Q1: Kya Node.js aur browser same JavaScript engine use karte hain?**
A: Chrome aur Node.js dono V8 engine use karte hain. Lekin "engine" sirf core language (variables, functions, objects, etc.) chalata hai. Extra APIs (`document` browser mein, `fs`/`process` Node.js mein) engine ka part nahi hain — wo runtime environment provide karta hai.

**Q2: Runtime environment kya hota hai?**
A: Runtime environment JavaScript engine + host-provided extra APIs ka combination hota hai. Jaise browser environment `window`, `document`, `fetch` deta hai; Node.js environment `fs`, `process`, `require` deta hai. Same JS engine, different environment, different capabilities.

## 17. Chapter Summary

JavaScript engine (jaise V8) sirf core language execute karta hai. Extra capabilities (DOM access browser mein, file system Node.js mein) runtime environment provide karta hai. Isi wajah se same JavaScript, different environments mein different superpowers rakhti hai.

## 18. Revision Checklist

- [ ] Runtime environment aur JavaScript engine ka difference clear hai.
- [ ] Browser vs Node.js ke global objects (`window` vs `process`/`global`) yaad hain.
- [ ] Isomorphic JavaScript ka concept samajh gaya.

---

**Next:** [04-Syntax-and-Statements.md](./04-Syntax-and-Statements.md)
