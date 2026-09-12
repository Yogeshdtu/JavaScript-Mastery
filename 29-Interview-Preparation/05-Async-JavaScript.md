# Interview Prep: Async JavaScript

**Module:** 29-Interview-Preparation
**Previous:** [04-Objects-and-Prototypes.md](./04-Objects-and-Prototypes.md)
**Next:** [06-DOM-and-Browser.md](./06-DOM-and-Browser.md)

---

**Q1: JavaScript single-threaded hai — iska practical matlab kya hai?**
A: Ek hi Call Stack hai — ek time pe sirf ek operation execute hoti hai. Slow operations (network, timers) ko async handle karna padta hai, warna poori application freeze ho jaati. (Detail: [13-Asynchronous-JavaScript/01-Sync-vs-Async.md](../13-Asynchronous-JavaScript/01-Sync-vs-Async.md))

**Q2: Promise ke 3 states kya hain?**
A: Pending (in-progress), Fulfilled (success), Rejected (failure). Ek baar settle hone ke baad state permanently fix ho jaati hai. (Detail: [13-Asynchronous-JavaScript/03-Promises-Deep-Dive.md](../13-Asynchronous-JavaScript/03-Promises-Deep-Dive.md))

**Q3: `Promise.all()` aur `Promise.allSettled()` mein kya difference hai?**
A: `all()` fail-fast hai — ek bhi reject hone pe poora reject ho jaata hai. `allSettled()` kabhi reject nahi hota, sabke results (fulfilled/rejected) collect karta hai. (Detail: [13-Asynchronous-JavaScript/04-Promise-Combinators.md](../13-Asynchronous-JavaScript/04-Promise-Combinators.md))

**Q4: `async` function hamesha kya return karti hai?**
A: Hamesha ek Promise — chahe tum plain value return karo, wo automatically Promise mein wrap ho jaati hai. (Detail: [14-Promises-and-Async-Await/01-Async-Await-Basics.md](../14-Promises-and-Async-Await/01-Async-Await-Basics.md))

**Q5: `await` poore program ko block karta hai?**
A: Nahi — sirf us specific `async` function ke execution ko pause karta hai. Baaki application (main thread) turant chalta rehta hai. (Detail: same as above)

**Q6: Async/await mein errors kaise handle kiye jaate hain?**
A: Normal `try/catch` se — jab `await` ki gayi Promise reject hoti hai, wo error automatically throw ban jaata hai us point pe, jise `try/catch` pakad sakta hai. (Detail: [14-Promises-and-Async-Await/02-Error-Handling-in-Async-Code.md](../14-Promises-and-Async-Await/02-Error-Handling-in-Async-Code.md))

**Q7: Sequential vs parallel async execution mein kya difference hai?**
A: Sequential `await` operations ek-ek karke chalti hain (total time = sum). Parallel (`Promise.all()`) sabko ek saath start karti hai (total time = max). Independent operations parallel karni chahiye performance ke liye. (Detail: [14-Promises-and-Async-Await/03-Sequential-vs-Parallel-Execution.md](../14-Promises-and-Async-Await/03-Sequential-vs-Parallel-Execution.md))

**Q8: Macrotask aur Microtask queue mein kya difference hai?**
A: Macrotask (setTimeout, DOM events) aur Microtask (Promises, async/await). Rule: har macrotask ke baad, **poori** microtask-queue drain hoti hai, tabhi next macrotask chalta hai. Isi liye `Promise.resolve().then()` hamesha `setTimeout(fn, 0)` se pehle chalta hai. (Detail: [15-Event-Loop/02-Task-Queue-vs-Microtask-Queue.md](../15-Event-Loop/02-Task-Queue-vs-Microtask-Queue.md))

**Q9: Ye code kya print karega?**
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```
A: `A, D, C, B` — sync code pehle, phir microtask, phir macrotask. (Detail: [15-Event-Loop/03-Event-Loop-Step-By-Step-Examples.md](../15-Event-Loop/03-Event-Loop-Step-By-Step-Examples.md))

**Q10: `fetch()` HTTP 404 pe reject hoti hai?**
A: Nahi! `fetch()` sirf network-failures pe reject hota hai. HTTP error status-codes (4xx/5xx) bhi "successful fetch" mane jaate hain — manually `response.ok` check karna zaroori hai. (Detail: [16-Fetch-and-APIs/02-Fetch-API.md](../16-Fetch-and-APIs/02-Fetch-API.md))

**Q11: Callback Hell kya hai?**
A: Deeply nested dependent async callbacks jo code ko "pyramid of doom" bana dete hain — readability, error-handling, maintainability sab suffer karte hain. Promises/async-await isi problem ko solve karte hain. (Detail: [13-Asynchronous-JavaScript/02-Callbacks-and-Callback-Hell.md](../13-Asynchronous-JavaScript/02-Callbacks-and-Callback-Hell.md))

---

**Next:** [06-DOM-and-Browser.md](./06-DOM-and-Browser.md)
