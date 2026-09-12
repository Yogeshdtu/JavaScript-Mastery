# Async/Await Basics

**Module:** 14-Promises-and-Async-Await
**Difficulty:** 🔴 Advanced
**Previous:** [13-Asynchronous-JavaScript/04-Promise-Combinators.md](../13-Asynchronous-JavaScript/04-Promise-Combinators.md)
**Next:** [02-Error-Handling-in-Async-Code.md](./02-Error-Handling-in-Async-Code.md)

---

## 1. Learning Objectives

- `async`/`await` syntax se Promises ko "synchronous-looking" code mein likhna.
- `async` function ka return-value behavior samajhna.
- `await` ka exact execution-pausing mechanism samajhna.

## 2. Prerequisites

Module 13 complete (especially Promises deep dive).

## 3. Concept in Simple Hinglish

`async`/`await` Promises ke upar ek **syntactic sugar** hai jo async code ko **synchronous jaisa dikhne/padhne** deta hai — bina `.then()` chains ke. `async` keyword function ko "special" banata hai, aur `await` us function ke andar kisi Promise ke resolve hone ka **wait** karta hai bina baaki application ko block kiye.

## 4. Technical Explanation

**`async function`:** Hamesha ek **Promise return karti hai** — chahe tum manually Promise return karo ya normal value, wo automatically Promise mein wrap ho jaati hai.

**`await`:** Sirf `async` function ke andar use ho sakta hai. Ye function ke execution ko **pause** karta hai us line pe jab tak Promise settle na ho jaaye — lekin ye poore program ko block nahi karta, sirf us specific `async` function ke execution ko.

## 5. Syntax

```js
async function myFunction() {
  const result = await somePromise;
  return result;
}

// Arrow function version
const myFunc = async () => {
  const result = await somePromise;
  return result;
};
```

## 6. Basic Examples

```js
function delay(value, ms) {
  return new Promise(resolve => setTimeout(() => resolve(value), ms));
}

async function greet() {
  const message = await delay("Hello!", 1000);
  console.log(message); // "Hello!" (after 1 second)
}

greet();
```

## 7. Detailed Examples

**`async` functions always return a Promise:**
```js
async function getNumber() {
  return 42; // A plain value...
}

console.log(getNumber()); // Promise {<fulfilled>: 42} — automatically wrapped!

getNumber().then(value => console.log(value)); // 42
```

**Rewriting the Promise-chain example from Module 13 using async/await:**
```js
function getUser(id) {
  return new Promise(resolve => setTimeout(() => resolve({ id, name: "Riya" }), 300));
}
function getOrders(userId) {
  return new Promise(resolve => setTimeout(() => resolve(["Order1", "Order2"]), 300));
}
function getOrderDetails(orderId) {
  return new Promise(resolve => setTimeout(() => resolve({ orderId, amount: 500 }), 300));
}

// ⚠️ Promise chaining version (from Module 13):
// getUser(1).then(user => getOrders(user.id)).then(orders => getOrderDetails(orders[0])).then(...)

// ✅ async/await version — reads like synchronous code!
async function processOrder() {
  const user = await getUser(1);
  console.log("User:", user);

  const orders = await getOrders(user.id);
  console.log("Orders:", orders);

  const details = await getOrderDetails(orders[0]);
  console.log("Details:", details);

  return details;
}

processOrder();
```
Notice karo — koi `.then()` nesting nahi, code **top-to-bottom, sequential** lagta hai — jabki actually async hai, bilkul waise hi jaise Promise-chain version.

**`await` pauses ONLY the async function, NOT the whole program:**
```js
async function slowTask() {
  console.log("Task started");
  await delay(null, 2000);
  console.log("Task finished");
}

console.log("Before calling slowTask");
slowTask();
console.log("After calling slowTask"); // This runs BEFORE "Task finished"!

// Output order:
// "Before calling slowTask"
// "Task started"
// "After calling slowTask"   <- Main program continues immediately!
// "Task finished"             <- (after 2 seconds)
```
Ye critical hai samajhna — `await` sirf `slowTask()` function ke andar ka execution pause karta hai, **poori application ko block nahi karta**. Baaki synchronous code (`console.log("After calling slowTask")`) turant chalta rehta hai.

**Multiple sequential awaits vs single combined wait — a performance consideration (preview of next-next chapter):**
```js
// ⚠️ This runs sequentially — takes 300 + 300 = 600ms even though they don't depend on each other
async function sequential() {
  const a = await delay("A", 300);
  const b = await delay("B", 300);
  return [a, b];
}
// Full detail on fixing this — Module 14, chapter 03
```

## 8. Mental Model

`async`/`await` ko socho ek **"pause button jo sirf apni khud ki video pe kaam karta hai"** — jab `await` hit hota hai, wahi specific function "pause" hota hai (jaise video pause karna), lekin dusri videos (baaki program) chalti rehti hain independently. Jab awaited Promise ready hoti hai, wahi function "resume" hota hai exactly wahi se jaha ruka tha.

## 9. What Happens Internally?

`async function` internally generator-jaisa mechanism use karta hai (Module 10, chapter 05 ke generators se conceptually related) — engine function ko `await` points pe pause kar deta hai aur us Promise ke resolve hone ka wait ek **microtask** ki tarah karta hai (Module 15 mein exact mechanism). Jab Promise resolve hoti hai, function ka execution wahi se **resume** hota hai jaha `await` tha, remaining code continue karta hai.

## 10. Common Mistakes

- `await` ko non-async function ke andar use karne ki koshish karna — `SyntaxError` (except top-level await in modules, Module 10 chapter 07 se yaad karo).
- `async` function se return hone wali value ko directly (bina `.then()`/`await` ke) use karne ki koshish karna — hamesha Promise milta hai.
- Independent (non-dependent) async operations ko sequential `await` se likhna jab parallel execution better performance deta — next chapter mein detail.

## 11. Edge Cases

```js
async function example() {
  console.log("1");
  await null; // Even awaiting a non-promise value still "yields" to the microtask queue!
  console.log("2");
}
example();
console.log("3");
// Output: 1, 3, 2 — because `await` always defers execution, even for immediately-resolved values
```

## 12. Real-World Usage

`async`/`await` aaj almost universally use hota hai modern JavaScript/TypeScript codebases mein — API calls (Module 16), database queries (Module 20), file operations (Module 18) sab jagah. Ye Promises ke upar ek readability layer hai, replace nahi karta — samajhna zaroori hai ki neeche Promises hi kaam kar rahi hain.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** `async`/`await` — better readability, easier debugging (linear stack traces), easier error handling (next chapter's `try/catch`).
**✅ Still valid:** Promise `.then()` chains — jab functional/chaining style prefer ho, ya jab combinators (`Promise.all`, etc.) ke saath combine karna ho.

## 14. Practice Questions

1. `async` function hamesha kya return karti hai?
2. `await` poore program ko pause karta hai ya sirf us function ko?
3. `await` ke bina `async` function ke andar `await` keyword use karna kya error dega?

## 15. Challenge

`delay()` helper function use karke ek `async function makeCoffee()` likho jo sequentially 3 steps simulate kare ("Boiling water" → "Adding coffee" → "Coffee ready"), har step ke beech ek chhota delay ke saath, `await` use karke.

## 16. Interview Questions

**Q1: `async`/`await` Promises se kaise related hai — kya ye Promises ko replace karta hai?**
A: `async`/`await` Promises ko **replace nahi** karta — ye Promises ke upar **syntactic sugar** hai jo async code ko synchronous-jaisi, zyada readable syntax mein likhne deta hai. `async` function internally hamesha ek Promise return karti hai, aur `await` keyword ek Promise ke resolve hone ka wait karta hai bina `.then()` callback likhe. Neeche machinery Promises hi hai — `async`/`await` sirf usse likhne/padhne ka behtar tareeka deta hai.

**Q2: `await` execution ko kaise pause karta hai — kya ye poori application ko block kar deta hai?**
A: Nahi, `await` sirf us **specific `async` function ke execution ko pause** karta hai jahan wo likha gaya hai — poori JavaScript application (main thread) block nahi hoti. Jab `await` ek Promise ka wait kar raha hota hai, baaki synchronous code (function ke bahar) turant chalta rehta hai. Jab awaited Promise settle ho jaati hai, us specific `async` function ka execution us point se **resume** hota hai (ek microtask ki tarah scheduled), jabki application baaki sab kaam continue karti rehti hai.

## 17. Chapter Summary

`async`/`await` Promises ko synchronous-looking syntax deta hai. `async` functions hamesha Promise return karti hain. `await` sirf apne containing `async` function ko pause karta hai, poore program ko nahi — baaki code turant execute hota rehta hai. Ye Promise chains ka ek readable alternative hai, replacement nahi.

## 18. Revision Checklist

- [ ] `async` function ka "always returns a Promise" rule yaad hai.
- [ ] `await` ka function-scoped-pausing (not global-blocking) behavior clear hai.
- [ ] Promise-chain code ko async/await mein convert kar sakta hoon.

---

**Next:** [02-Error-Handling-in-Async-Code.md](./02-Error-Handling-in-Async-Code.md)
