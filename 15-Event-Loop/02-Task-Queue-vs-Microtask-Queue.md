# Task Queue vs Microtask Queue

**Module:** 15-Event-Loop
**Difficulty:** 🔴 Advanced
**Previous:** [01-Call-Stack-Heap-and-WebAPIs.md](./01-Call-Stack-Heap-and-WebAPIs.md)
**Next:** [03-Event-Loop-Step-By-Step-Examples.md](./03-Event-Loop-Step-By-Step-Examples.md)

---

## 1. Learning Objectives

- Macrotask (Task) Queue aur Microtask Queue ka difference samajhna.
- Priority order samajhna — microtasks macrotasks se pehle kyun chalte hain.
- Ye samajhna ki kaunse operations kaunsi queue mein jaate hain.

## 2. Prerequisites

[01-Call-Stack-Heap-and-WebAPIs.md](./01-Call-Stack-Heap-and-WebAPIs.md)

## 3. Concept in Simple Hinglish

Jab async operations complete hoti hain, unke callbacks turant Call Stack pe nahi jaate — pehle ek **queue** mein wait karte hain. JavaScript ke paas **do alag queues** hain — **Macrotask Queue** (`setTimeout`, DOM events ke liye) aur **Microtask Queue** (Promises ke liye) — aur Microtask Queue ko **higher priority** milti hai.

## 4. Technical Explanation

**Macrotask Queue (aka "Task Queue" or "Callback Queue"):**
- `setTimeout`/`setInterval` callbacks
- DOM event callbacks (click, scroll, etc.)
- I/O operations (Node.js)

**Microtask Queue:**
- Promise `.then()`/`.catch()`/`.finally()` callbacks
- `async`/`await` continuations (jo internally Promises use karte hain)
- `queueMicrotask()` (explicit API)

**The critical rule:** Har baar jab Call Stack empty hota hai, Event Loop **pehle poori Microtask Queue ko completely empty** karta hai (including microtasks jo dusre microtasks ke andar se add hue) — **uske baad hi** ek single macrotask process karta hai. Ye cycle repeat hota hai.

## 5. Syntax

Ye conceptual hai — koi direct syntax nahi, but ye order determine karta hai:
```js
setTimeout(() => console.log("macrotask"), 0);
Promise.resolve().then(() => console.log("microtask"));
```

## 6. Basic Examples

```js
console.log("1");                                    // Synchronous
setTimeout(() => console.log("2"), 0);                 // Macrotask
Promise.resolve().then(() => console.log("3"));         // Microtask
console.log("4");                                       // Synchronous

// Output: 1, 4, 3, 2
// Synchronous code first, then ALL microtasks, then macrotasks
```

## 7. Detailed Examples

**Step-by-step trace of the example above:**
```
1. console.log("1") → Call Stack executes directly → prints "1"
2. setTimeout(...) → callback handed to Web APIs (timer starts), goes to MACROTASK queue when ready
3. Promise.resolve().then(...) → the .then() callback goes to MICROTASK queue (Promise already resolved)
4. console.log("4") → Call Stack executes directly → prints "4"
5. Call Stack is now EMPTY — synchronous code is done.
6. Event Loop checks: Microtask Queue has items? YES → run ALL of them → prints "3"
7. Microtask Queue is now empty.
8. Event Loop checks: Macrotask Queue has items? YES → run ONE → prints "2"
```

**Microtasks can "starve" macrotasks if they keep adding more microtasks:**
```js
function addMicrotask() {
  Promise.resolve().then(() => {
    console.log("Microtask running");
    addMicrotask(); // Adds ANOTHER microtask before this one finishes!
  });
}
addMicrotask();

setTimeout(() => console.log("This may be significantly delayed!"), 0);
// The setTimeout callback has to wait until the microtask queue is FULLY empty —
// if microtasks keep adding more microtasks, this could take a very long time!
```
Ye ek real-world gotcha hai — agar microtasks continuously naye microtasks add karte rahen, macrotasks (jaise `setTimeout`) indefinitely delay ho sakte hain, kyunki Event Loop macrotask ki taraf tabhi badhta hai jab microtask queue **completely** empty ho jaaye.

**A comprehensive mixed example — the classic interview question:**
```js
console.log("Script start");

setTimeout(() => console.log("setTimeout"), 0);

Promise.resolve()
  .then(() => console.log("Promise 1"))
  .then(() => console.log("Promise 2"));

console.log("Script end");

// Output:
// Script start
// Script end
// Promise 1
// Promise 2
// setTimeout
```
**Explanation:** Synchronous code (`Script start`, `Script end`) hamesha pehle chalta hai. Phir microtask queue completely drain hoti hai (`Promise 1` phir `Promise 2` — chained `.then()` bhi microtask hi hai, isliye macrotask se pehle chalega). Sabse aakhir mein macrotask (`setTimeout`) chalta hai.

**`async`/`await` and the microtask queue:**
```js
async function asyncFn() {
  console.log("A");
  await null; // Yields to microtask queue here
  console.log("B"); // This continuation is a MICROTASK
}

console.log("Start");
asyncFn();
console.log("End");

// Output: Start, A, End, B
// (asyncFn runs synchronously until 'await', then the rest is scheduled as a microtask)
```

## 8. Mental Model

Socho ek **restaurant with VIP and regular queues**. Microtask Queue VIP hai — har baar jab kitchen (Call Stack) free hoti hai, saare VIP orders (microtasks) **completely** serve kiye jaate hain pehle, chahe naye VIP orders beech mein aate rahen. Sirf jab VIP queue **poori tarah khaali** ho, ek regular order (macrotask) serve hota hai — phir se VIP queue check hoti hai.

## 9. What Happens Internally?

Event Loop ek continuous cycle hai:
1. Ek macrotask execute karo (agar Call Stack empty hai).
2. Uske baad, **saari** microtask queue ko drain karo (jab tak completely empty na ho jaaye).
3. Rendering update karo (agar zaroorat ho, browser mein).
4. Step 1 pe wapas jao.

Ye "microtasks-fully-drained-before-next-macrotask" rule ECMAScript aur HTML specifications dono mein explicitly defined hai.

## 10. Common Mistakes

- `setTimeout(fn, 0)` aur `Promise.resolve().then(fn)` ko same-priority samajhna — Promise **hamesha pehle** chalega.
- Microtask chains ko infinite banana (jaise upar wala starvation example) — real bugs create kar sakta hai jaha UI updates ya timers indefinitely delay ho jaate hain.
- Async/await continuations ko "synchronous jaisi turant chalne wali" samajhna — actually wo bhi microtask queue use karte hain.

## 11. Edge Cases

```js
setTimeout(() => console.log("timeout 1"), 0);
setTimeout(() => console.log("timeout 2"), 0);
Promise.resolve().then(() => console.log("promise"));
// Output: promise, timeout 1, timeout 2
// (all microtasks drain before EVEN ONE macrotask runs, and macrotasks run in their own FIFO order)
```

## 12. Real-World Usage

Ye knowledge critical hai jab debug kar rahe ho ki UI update kyun expected order mein nahi ho raha, ya jab React jaisi libraries ke internal scheduling (batching updates using microtasks) samajhne ki koshish kar rahe ho. Performance-critical code mein microtask-starvation avoid karna important hai.

## 13. Comparison With Related Concepts

| Aspect | Macrotask Queue | Microtask Queue |
|--------|---------------------|----------------------|
| Sources | `setTimeout`, DOM events, I/O | Promises, `async/await`, `queueMicrotask` |
| Priority | Lower | Higher — always fully drained first |
| Processed per cycle | ONE per Event Loop tick | ALL, until completely empty |

## 14. Practice Questions

1. Same-time `setTimeout(fn, 0)` aur `Promise.resolve().then(fn)` — kaunsa pehle chalega?
2. Event Loop ek macrotask ke baad kya karta hai, agle macrotask se pehle?
3. Microtask "starvation" kya hai?

## 15. Challenge

Ek code snippet likho jisme 2 `setTimeout`s aur 3 chained `.then()` calls ho, sabko mix order mein likho, aur predict karo exact console output order — phir verify karo.

## 16. Interview Questions

**Q1: Macrotask aur microtask queue mein kya difference hai, aur inka priority order kya hai?**
A: Macrotask Queue mein `setTimeout`/`setInterval` callbacks, DOM events, aur I/O operations jaati hain. Microtask Queue mein Promise callbacks (`.then`/`.catch`/`.finally`) aur `async`/`await` continuations jaati hain. **Priority rule:** Har baar Call Stack empty hone pe, Event Loop pehle **poori Microtask Queue ko completely drain** karta hai (naye add hue microtasks bhi shamil), uske **baad hi** ek single macrotask process karta hai. Isi wajah se `Promise.resolve().then()` hamesha `setTimeout(fn, 0)` se pehle chalta hai.

**Q2: `Promise.resolve().then(fn)` `setTimeout(fn, 0)` se pehle kyun chalta hai, chahe dono "immediately" schedule hue ho?**
A: Ye JavaScript ke Event Loop ke priority-rule ki wajah se hai — Promise callbacks **microtask queue** mein jaate hain, `setTimeout` callbacks **macrotask queue** mein. Event Loop specification ke hisaab se, Call Stack empty hone ke baad, saari microtasks (chahe kitni bhi hon, chahe naye add hote rahen) **poori tarah process** ki jaati hain macrotask queue se koi bhi item process karne se **pehle**. Isliye `0ms` delay hone ke bawajood, `setTimeout` callback hamesha microtask ke baad hi chalega.

## 17. Chapter Summary

JavaScript mein 2 async callback queues hain — Macrotask (timers, events, I/O) aur Microtask (Promises, async/await). Event Loop rule: har macrotask ke baad, **poori** microtask queue drain hoti hai before next macrotask. Ye priority order predict karne ke liye critical hai ki async code kis order mein execute hoga.

## 18. Revision Checklist

- [ ] Macrotask vs microtask sources yaad hain.
- [ ] "Full microtask drain before next macrotask" rule clear hai.
- [ ] Mixed setTimeout/.then() examples trace kar sakta hoon.

---

**Next:** [03-Event-Loop-Step-By-Step-Examples.md](./03-Event-Loop-Step-By-Step-Examples.md)
