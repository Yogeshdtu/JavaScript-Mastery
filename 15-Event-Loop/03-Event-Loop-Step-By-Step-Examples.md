# Event Loop — Step-by-Step Examples

**Module:** 15-Event-Loop
**Difficulty:** 🔴 Advanced
**Previous:** [02-Task-Queue-vs-Microtask-Queue.md](./02-Task-Queue-vs-Microtask-Queue.md)
**Next:** [16-Fetch-and-APIs/01-HTTP-Basics.md](../16-Fetch-and-APIs/01-HTTP-Basics.md)

---

## 1. Learning Objectives

- Poora Event Loop mechanism ko end-to-end trace karna complex examples ke saath.
- Rendering ka Event Loop ke saath interaction samajhna.
- Interview-level tricky async output-prediction questions solve karna.

## 2. Prerequisites

Module 15, chapters 01-02.

## 3. Concept in Simple Hinglish

Ab jab humne Call Stack, Web APIs, Macrotask Queue, aur Microtask Queue individually samajh liye hain, is chapter mein hum unhe **combine** karke complex, real-world-jaisi examples trace karenge — exactly jaisa interview mein pucha jaata hai.

## 4. Technical Explanation

**Complete Event Loop Algorithm:**
```
LOOP FOREVER:
  1. If Call Stack is empty:
     a. Take ONE task from the Macrotask Queue (if any) and push it to the Call Stack. Execute it fully.
     b. After that macrotask completes, drain the ENTIRE Microtask Queue
        (including any new microtasks added during this draining).
     c. (Browser) Possibly perform a rendering update.
  2. Repeat.
```

Important: **initial script execution** (the top-level synchronous code) is itself treated somewhat like the "first macrotask" for this purpose.

## 5. Syntax

Conceptual chapter — focus hai tracing examples pe.

## 6. Basic Examples

Refer Module 15, chapters 01-02 ke examples — ye chapter unhe combine karke bade examples banayega.

## 7. Detailed Examples

**Example 1 — The classic comprehensive trace:**
```js
console.log("1: Sync");

setTimeout(() => console.log("2: Macrotask (setTimeout)"), 0);

Promise.resolve().then(() => console.log("3: Microtask (Promise 1)"));

async function asyncFunc() {
  console.log("4: Sync (inside async, before await)");
  await null;
  console.log("5: Microtask (after await)");
}
asyncFunc();

Promise.resolve().then(() => console.log("6: Microtask (Promise 2)"));

console.log("7: Sync");
```

**Step-by-step trace:**
```
Phase 1 — Synchronous execution (Call Stack):
  "1: Sync" → printed
  setTimeout(...) → callback registered with Web APIs → MACROTASK QUEUE (later)
  Promise.resolve().then(...) → callback → MICROTASK QUEUE
  asyncFunc() is called:
    "4: Sync (inside async, before await)" → printed (this part IS synchronous!)
    await null → pauses asyncFunc, schedules the REST of it as a MICROTASK
  Promise.resolve().then(...) → callback → MICROTASK QUEUE
  "7: Sync" → printed

  Call Stack is now empty. Synchronous phase done.

Phase 2 — Drain the ENTIRE Microtask Queue (in order added):
  "3: Microtask (Promise 1)" → printed
  "5: Microtask (after await)" → printed
  "6: Microtask (Promise 2)" → printed

  Microtask Queue is now empty.

Phase 3 — Process ONE Macrotask:
  "2: Macrotask (setTimeout)" → printed
```

**Final Output:**
```
1: Sync
4: Sync (inside async, before await)
7: Sync
3: Microtask (Promise 1)
5: Microtask (after await)
6: Microtask (Promise 2)
2: Macrotask (setTimeout)
```

**Example 2 — Nested setTimeout and Promises:**
```js
setTimeout(() => {
  console.log("Timeout 1");
  Promise.resolve().then(() => console.log("Promise inside Timeout 1"));
}, 0);

setTimeout(() => {
  console.log("Timeout 2");
}, 0);

Promise.resolve().then(() => console.log("Promise A"));
```

**Trace:**
```
Sync phase: nothing printed directly (all wrapped in setTimeout/then)
Microtask drain: "Promise A"
Macrotask 1 (Timeout 1's callback):
  "Timeout 1" printed
  A NEW microtask is added (Promise inside Timeout 1)
  Before moving to the NEXT macrotask, drain microtasks again: "Promise inside Timeout 1"
Macrotask 2 (Timeout 2's callback):
  "Timeout 2" printed
```
**Final Output:** `Promise A`, `Timeout 1`, `Promise inside Timeout 1`, `Timeout 2`

**Key insight:** Microtask queue ko **har macrotask ke baad** drain kiya jaata hai — sirf ek baar shuru mein nahi. Isi liye "Promise inside Timeout 1" "Timeout 2" se pehle aata hai, chahe dono `setTimeout`s originally same time (`0ms`) pe schedule hue the.

**Example 3 — Rendering consideration (browser-specific, conceptual):**
```js
// In browsers, after microtasks drain, the browser MAY perform a rendering update
// before processing the next macrotask (especially relevant for requestAnimationFrame).
// This is why heavy microtask chains can delay visual updates — the browser can't "breathe."
```

## 8. Mental Model

Poore Event Loop ko socho ek **strict factory manager** jo follow karta hai: "Pehle current order (macrotask) poora karo. Fir jitne bhi 'urgent notes' (microtasks) aaye hain unhe SAB clear karo — chahe clear karte waqt naye notes bhi aa jaayen. Sab notes clear hone ke baad hi, agli order (next macrotask) lo."

## 9. What Happens Internally?

Ye exact mechanism ECMAScript specification (Jobs and Job Queues) aur HTML specification (Event Loop processing model) mein formally defined hai. Har JavaScript engine/runtime (V8 in Chrome/Node, SpiderMonkey in Firefox) is specification ko implement karta hai, isliye behavior consistent hai across environments (minor host-specific differences ke saath, jaise rendering timing browsers mein).

## 10. Common Mistakes

- Sochna ki microtasks sirf "ek round" drain hote hain — actually naye add hue microtasks bhi usi drain-cycle mein process hote hain jab tak queue truly empty na ho.
- Multiple `setTimeout(fn, 0)` calls ko "same time execute honge" samajhna — wo apni registration-order mein, ek-ek karke, apne beech microtask-drains ke saath chalte hain.
- Async function ke `await` se **pehle** wale code ko asynchronous samajhna — wo part **synchronous** hi chalta hai.

## 11. Edge Cases

`process.nextTick()` (Node.js-specific, not in browsers) ek aur bhi **higher priority** queue hai — even Promise microtasks se pehle chalta hai Node.js mein. Ye ek Node.js-specific detail hai jo Module 18 mein relevant ho sakta hai.

## 12. Real-World Usage

Ye deep understanding tab critical hoti hai jab tum complex async bugs debug kar rahe ho — jaise "UI update kyun late ho raha hai", ya "ye function calls kis order mein chal rahe hain jabki mujhe alag order expect tha". Senior-level interviews mein ye topic bahut commonly pucha jaata hai output-prediction questions ke through.

## 13. Comparison With Related Concepts

Is chapter ne Module 15 ke chapters 01-02 ko synthesize kiya — Call Stack + Web APIs + Macrotask/Microtask Queues + Event Loop rules, sab ek saath, real examples ke through.

## 14. Practice Questions

1. Do consecutive `setTimeout(fn, 0)` calls ke beech microtask queue kitni baar drain hoti hai (assuming no microtasks added inside them)?
2. Async function ke `await` se pehle wala code sync hai ya async?
3. Har macrotask ke baad kya hota hai, agle macrotask se pehle?

## 15. Challenge

Apna khud ka complex example likho (kam se kam 2 `setTimeout`s, 2 Promises, aur ek `async function` involve karke). Manually trace karo phases ke through (jaisa upar dikhaya), predict karo output, phir actual code run karke verify karo.

## 16. Interview Questions

**Q1: Ye code kya print karega, exact order mein? (Classic interview question)**
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```
A: Output hoga: `A`, `D`, `C`, `B`. Explanation: `console.log("A")` aur `console.log("D")` synchronous hain, turant Call Stack pe chalte hain. `setTimeout` ka callback Macrotask Queue mein jaata hai. `Promise.resolve().then()` ka callback Microtask Queue mein jaata hai. Call Stack empty hone ke baad, Event Loop pehle poori Microtask Queue drain karta hai (`C` prints), phir ek Macrotask process karta hai (`B` prints).

**Q2: Agar ek microtask apne andar se ek naya microtask add karta rahe, to kya hoga macrotasks ka?**
A: Agar microtasks continuously naye microtasks add karte rahen (jaise recursive `Promise.resolve().then()` chain jo khud ko baar-baar re-schedule karti hai), Event Loop **kabhi bhi macrotask queue ki taraf nahi badhega**, kyunki rule hai "microtask queue ko completely drain karo before moving to next macrotask" — agar queue kabhi empty hi na ho (naye items continuously aa rahe hon), macrotasks (jaise `setTimeout` callbacks, ya even UI rendering) indefinitely starve ho sakte hain. Ye ek real performance-hazard hai jo production code mein avoid karna chahiye.

## 17. Chapter Summary

Event Loop ek continuous cycle hai: synchronous code → poori microtask queue drain → ek macrotask → poori microtask queue drain (again) → agla macrotask → ... Ye pattern predict karne ke liye zaroori hai ki complex async code kis exact order mein execute hoga. Har macrotask ke baad microtask queue dobara poori drain hoti hai, sirf ek baar shuru mein nahi.

## 18. Revision Checklist

- [ ] Poora Event Loop cycle (macrotask → full microtask drain → repeat) trace kar sakta hoon.
- [ ] Multi-setTimeout + multi-Promise examples confidently solve kar sakta hoon.
- [ ] Microtask-starvation ka risk aur cause samajh gaya.

---

**Module 15 Complete!** Next Module: [16-Fetch-and-APIs/01-HTTP-Basics.md](../16-Fetch-and-APIs/01-HTTP-Basics.md)
