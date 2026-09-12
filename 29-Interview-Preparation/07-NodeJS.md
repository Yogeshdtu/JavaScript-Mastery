# Interview Prep: Node.js

**Module:** 29-Interview-Preparation
**Previous:** [06-DOM-and-Browser.md](./06-DOM-and-Browser.md)
**Next:** [08-Output-Based-Questions.md](./08-Output-Based-Questions.md)

---

**Q1: Node.js kya hai, browser JavaScript se kaise different hai?**
A: Node.js ek runtime environment hai jo JavaScript ko browser ke bahar chalata hai (V8 engine + libuv for non-blocking I/O). Browser environment DOM/window deta hai, Node environment `fs`/`process`/`http` deta hai. Core language same hai, extra APIs different. (Detail: [01-JavaScript-Foundations/03-How-JavaScript-Runs.md](../01-JavaScript-Foundations/03-How-JavaScript-Runs.md), [18-NodeJS/01-Node-Architecture-and-NPM.md](../18-NodeJS/01-Node-Architecture-and-NPM.md))

**Q2: `dependencies` aur `devDependencies` mein kya difference hai?**
A: `dependencies` production mein zaroori packages hain (jaise Express). `devDependencies` sirf development/testing ke liye (jaise Jest, ESLint) — production build mein skip ho sakte hain. (Detail: [18-NodeJS/01-Node-Architecture-and-NPM.md](../18-NodeJS/01-Node-Architecture-and-NPM.md))

**Q3: `package-lock.json` ka purpose kya hai?**
A: Exact resolved dependency-versions record karta hai — team-wide aur deployment-wide consistency ensure karta hai, "works on my machine" issues avoid karta hai. (Detail: same as above)

**Q4: Synchronous `fs` methods production servers mein risky kyun hain?**
A: Node single-threaded hai — sync operations poore thread ko block kar dete hain, jisse sabhi doosre incoming requests bhi delay ho jaate hain. Async APIs (`fs.promises`) use karni chahiye. (Detail: [18-NodeJS/02-File-System-and-Path.md](../18-NodeJS/02-File-System-and-Path.md))

**Q5: Environment variables kyun use karte hain?**
A: Secrets/configuration ko code se separate rakhte hain — security (source-control mein exposed nahi) aur flexibility (different environments mein same code, different config) ke liye. (Detail: [18-NodeJS/03-Process-and-Environment-Variables.md](../18-NodeJS/03-Process-and-Environment-Variables.md))

**Q6: Node.js Streams ka main advantage kya hai?**
A: Data ko chunks mein process karte hain, poori file memory mein load kiye bina — bade files (videos, logs) memory-efficiently handle karne ke liye essential. (Detail: [18-NodeJS/04-Events-Streams-and-Buffers.md](../18-NodeJS/04-Events-Streams-and-Buffers.md))

**Q7: `EventEmitter` mein `'error'` event listener na hone se kya risk hai?**
A: Agar `'error'` emit hota hai bina listener ke, Node.js process ko **crash** kar deta hai — hamesha ek `'error'` listener attach karna chahiye. (Detail: same as above)

**Q8: CommonJS aur ES Modules mein kya difference hai?**
A: CommonJS (`require`/`module.exports`) synchronous, Node-original system hai. ES Modules (`import`/`export`) asynchronous-capable, official, static-analysis-friendly hain. `package.json`'s `"type"` field decide karta hai kaunsa use ho raha hai. (Detail: [17-Modules/03-ESM-vs-CommonJS-and-Dynamic-Imports.md](../17-Modules/03-ESM-vs-CommonJS-and-Dynamic-Imports.md))

**Q9: Express middleware kya hai?**
A: `(req, res, next)` signature wale functions jo request-response cycle ke beech chalte hain — logging, auth, validation ke liye. `next()` control ko aage badhata hai. (Detail: [19-Backend-JavaScript/02-Middleware-and-REST-APIs.md](../19-Backend-JavaScript/02-Middleware-and-REST-APIs.md))

**Q10: Database transactions kyun important hain?**
A: Multi-step operations ko "all-or-nothing" banate hain — jaise bank-transfer mein deduct+add dono ya to poore hon ya koi bhi na ho, partial-failure se data-inconsistency avoid karte hain. (Detail: [20-Databases/03-ORMs-Transactions-Indexing.md](../20-Databases/03-ORMs-Transactions-Indexing.md))

---

**Next:** [08-Output-Based-Questions.md](./08-Output-Based-Questions.md)
