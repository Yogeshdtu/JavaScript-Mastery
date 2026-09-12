# Call Stack, Heap, and Web APIs

**Module:** 15-Event-Loop
**Difficulty:** 🔴 Advanced
**Previous:** [14-Promises-and-Async-Await/03-Sequential-vs-Parallel-Execution.md](../14-Promises-and-Async-Await/03-Sequential-vs-Parallel-Execution.md)
**Next:** [02-Task-Queue-vs-Microtask-Queue.md](./02-Task-Queue-vs-Microtask-Queue.md)

---

## 1. Learning Objectives

- JavaScript runtime ke major components identify karna: Call Stack, Heap, Web APIs.
- Ye samajhna ki async operations ko Call Stack se "bahar" kaise handle kiya jaata hai.
- Event Loop deep-dive ke liye foundation build karna.

## 2. Prerequisites

Module 08 chapter 03 (Execution Context/Call Stack), Module 13-14 (Async/Promises).

## 3. Concept in Simple Hinglish

JavaScript runtime (jaise browser ya Node.js) ke andar kai components milke kaam karte hain async behavior possible banane ke liye. **Call Stack** current execution track karta hai, **Heap** memory mein objects store karta hai, aur **Web APIs** (browser-provided) slow operations ko "background" mein handle karte hain jab tak JavaScript ka single thread free na ho.

## 4. Technical Explanation

**Call Stack:** (Module 08, chapter 03 se recap) — LIFO structure jo currently-executing functions track karta hai.

**Heap:** Ek large, mostly-unstructured memory region jaha **objects** store hote hain (arrays, objects, functions — Module 02 chapter 05 ke "reference types" yahi store hote hain).

**Web APIs (Browser):** Browser-provided capabilities jo JavaScript engine ka part **nahi** hain — `setTimeout`, `fetch`, DOM events, `localStorage`. Ye C++ (ya similar) mein implemented hoti hain browser ke andar, JavaScript engine ke bahar.

**Node.js equivalent:** `libuv` — ek C library jo Node.js ko non-blocking I/O (file system, network) provide karti hai, similar role Web APIs jaisa.

## 5. Syntax

Ye conceptual/architectural hai — koi direct syntax nahi.

## 6. Basic Examples

```js
console.log("1"); // Runs on Call Stack directly

setTimeout(() => {
  console.log("2"); // This callback is handed to Web APIs, comes back later
}, 1000);

console.log("3"); // Runs on Call Stack directly
```

## 7. Detailed Examples

**Tracing where each piece of code "lives" during execution:**
```js
console.log("Start");

setTimeout(() => {
  console.log("Timeout callback");
}, 2000);

fetch("https://api.example.com/data")
  .then(response => console.log("Fetch resolved"));

console.log("End");
```

**Step-by-step trace:**
```
1. "Start" logged — runs directly on Call Stack.
2. setTimeout() is called — Call Stack pushes/pops setTimeout() itself immediately.
   The TIMER (and its callback) is handed off to Web APIs — NOT the Call Stack.
   The Web API starts a 2000ms countdown, independent of the JS thread.
3. fetch() is called — Call Stack pushes/pops fetch() itself immediately.
   The network request is handed off to Web APIs (browser's networking layer).
4. "End" logged — runs directly on Call Stack.
5. Call Stack is now EMPTY. Main synchronous code has finished.
6. [Later] Network response arrives → the .then() callback moves to a queue → Event Loop
   pushes it onto the Call Stack when the stack is empty → "Fetch resolved" logged.
7. [Even later, ~2000ms] Timer expires → its callback moves to a queue → Event Loop
   pushes it onto the Call Stack when the stack is empty → "Timeout callback" logged.
```
**Output order:** "Start", "End", "Fetch resolved" (jab bhi network response aaye), "Timeout callback" (2 seconds baad) — exact order fetch-response-time pe depend karta hai, but "Start"/"End" **hamesha pehle** aayenge kyunki wo synchronous hain.

**Why the Heap matters for closures (connecting to Module 08, chapter 04):**
```js
function createCounter() {
  let count = 0; // This variable lives in the Heap (referenced by the closure)
  return function () {
    return ++count;
  };
}

const counter = createCounter(); // createCounter()'s Execution Context is popped from the Call Stack...
// ...but 'count' survives in the Heap because the returned function still references it!
console.log(counter()); // 1
```

## 8. Mental Model

Socho JavaScript runtime ek **restaurant** ki tarah:
- **Call Stack** = ek hi chef jo ek time pe ek hi dish bana sakta hai (single-threaded).
- **Heap** = storage room jaha ingredients (objects/data) rakhe jaate hain, jab zaroorat ho use karne ke liye.
- **Web APIs** = ek team of "outsourced helpers" (delivery service, ya slow-cooking oven) jo time-consuming kaam (timers, network requests) apne aap handle karte hain, chef ko free rakhte hue naye orders lene ke liye.

## 9. What Happens Internally?

Jab async function (jaise `setTimeout`) call hoti hai, ye Call Stack pe sirf **turant push aur pop** hoti hai (function call khud fast hai) — actual "waiting" kaam Web API ko **delegate** ho jaata hai. Jab Web API apna kaam complete kar leta hai (timer expire, response aa gaya), wo callback ko ek **queue** mein daal deta hai (next chapter mein detail), jaha se **Event Loop** usse Call Stack mein wapas laata hai jab stack empty ho.

## 10. Common Mistakes

- Web APIs ko "JavaScript engine ka part" samajhna — actually ye engine ke bahar, host environment (browser/Node.js) ka part hain.
- Sochna ki `setTimeout` callback "turant Call Stack pe wait karta hai" — actually turant Web APIs ko handoff ho jaata hai, Call Stack immediately free ho jaata hai.
- Heap aur Call Stack ke roles ko confuse karna — Stack "execution flow" track karta hai, Heap "data" store karta hai.

## 11. Edge Cases

Node.js mein Web APIs exist nahi karte (ye browser-specific hain) — inki jagah **libuv** hai jo similar role play karta hai (timers, file I/O, network) apne "thread pool" ke saath — concept same hai, implementation different.

## 12. Real-World Usage

Ye mental model debugging ke liye critical hai — jab tum samajhte ho ki async operations kaha "chal rahe" hain (Web APIs mein, na ki Call Stack pe), tumhe async bugs (jaise unexpected execution order) explain karna aasan ho jaata hai.

## 13. Comparison With Related Concepts

| Component | Role | Part of JS Engine? |
|-----------|------|--------------------------|
| Call Stack | Tracks currently executing code | ✅ Yes |
| Heap | Stores objects/data | ✅ Yes |
| Web APIs / libuv | Handles async operations (timers, network, I/O) | ❌ No — host environment provides this |

## 14. Practice Questions

1. `setTimeout()` call hote hi kya Call Stack pe rehta hai poora 2 seconds tak?
2. Web APIs JavaScript engine ka part hain ya nahi?
3. Node.js mein Web APIs ka equivalent kya hai?

## 15. Challenge

3 console.logs aur beech mein ek `setTimeout(fn, 0)` likho. Manually trace karo ki har line Call Stack, Web API, ya queue mein kab hai, phir predict karo final output order.

## 16. Interview Questions

**Q1: `setTimeout` call hone ke baad uska callback kaha "wait" karta hai jab tak timer expire na ho?**
A: `setTimeout()` khud Call Stack pe turant execute hoke pop ho jaata hai — ye sirf ek "registration" call hai. Actual timer aur uska callback **Web APIs** (browser mein) ya **libuv** (Node.js mein) ko handoff ho jaate hain, jo JavaScript engine ke bahar, host environment ka part hain. Timer waha independently count-down karta hai. Jab timer expire hota hai, callback ek queue mein move ho jaata hai, jaha se Event Loop usse Call Stack pe le aata hai jab stack empty ho — Call Stack khud kabhi "wait" nahi karta.

**Q2: Call Stack aur Heap mein kya difference hai?**
A: **Call Stack** ek LIFO structure hai jo track karta hai kaunsa function currently execute ho raha hai aur kis order mein calls hue — ye "execution flow" manage karta hai. **Heap** ek larger, less-structured memory region hai jaha actual data (objects, arrays, functions — reference types) store hoti hai. Stack "kya ho raha hai" track karta hai, Heap "data kaha hai" store karta hai. Closures (Module 08, chapter 04) Heap mein data ko "zinda" rakhte hain chahe unka original Execution Context Stack se pop ho gaya ho.

## 17. Chapter Summary

JavaScript runtime mein Call Stack (execution tracking), Heap (data storage), aur Web APIs/libuv (async operation handling, engine ke bahar) milke kaam karte hain. Async function calls turant Call Stack se pop ho jaate hain, actual waiting Web APIs mein hoti hai — ye samajhna Event Loop (next chapter) ka foundation hai.

## 18. Revision Checklist

- [ ] Call Stack, Heap, Web APIs ka alag-alag role clear hai.
- [ ] `setTimeout` call turant Call Stack se pop hone ka mechanism samajh gaya.
- [ ] Node.js ka libuv-equivalent yaad hai.

---

**Next:** [02-Task-Queue-vs-Microtask-Queue.md](./02-Task-Queue-vs-Microtask-Queue.md)
