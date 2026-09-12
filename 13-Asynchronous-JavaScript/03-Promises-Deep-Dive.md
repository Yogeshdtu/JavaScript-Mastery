# Promises Deep Dive

**Module:** 13-Asynchronous-JavaScript
**Difficulty:** 🔴 Advanced
**Previous:** [02-Callbacks-and-Callback-Hell.md](./02-Callbacks-and-Callback-Hell.md)
**Next:** [04-Promise-Combinators.md](./04-Promise-Combinators.md)

---

## 1. Learning Objectives

- Promise ke 3 states samajhna.
- `.then()`, `.catch()`, `.finally()` se Promise chains banana.
- Error propagation ka mechanism Promises mein samajhna.
- Callback Hell ko Promises se solve karna.

## 2. Prerequisites

Module 13, chapters 01-02.

## 3. Concept in Simple Hinglish

**Promise** ek object hai jo represent karta hai kisi async operation ka **eventual result** — abhi turant nahi mila, lekin "promise" hai ki future mein ya to value milegi (success) ya reason milegi kyun fail hua (failure). Ye "restaurant ka token number" jaisa hai — turant khaana nahi milta, lekin ek token milta hai jo eventually khaana (ya "sorry, out of stock" message) dega.

## 4. Technical Explanation

**Promise ke 3 States:**
1. **Pending:** Initial state — operation abhi complete nahi hua.
2. **Fulfilled:** Operation successfully complete hua — value available hai.
3. **Rejected:** Operation fail hua — error/reason available hai.

Ek Promise **permanently settle** ho jaata hai (fulfilled ya rejected) — ek baar settle hone ke baad state kabhi change nahi hoti.

**Key Methods:**
- `.then(onFulfilled, onRejected)`: Fulfilled/rejected value handle karta hai, **naya Promise return karta hai** (chaining ke liye).
- `.catch(onRejected)`: Sirf rejection handle karta hai — `.then(null, onRejected)` ka shorthand.
- `.finally(callback)`: Fulfilled ya rejected — dono cases mein chalta hai (cleanup ke liye).

## 5. Syntax

```js
const promise = new Promise((resolve, reject) => {
  // async operation
  if (success) {
    resolve(value);
  } else {
    reject(error);
  }
});

promise
  .then(value => { /* handle success */ })
  .catch(error => { /* handle error */ })
  .finally(() => { /* cleanup, always runs */ });
```

## 6. Basic Examples

```js
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Success!");
  }, 1000);
});

myPromise.then(result => console.log(result)); // "Success!" (after 1 second)
```

## 7. Detailed Examples

**Creating a Promise-based function (converting a callback-style function):**
```js
function fetchUserPromise(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (id > 0) {
        resolve({ id, name: "User " + id });
      } else {
        reject(new Error("Invalid user ID"));
      }
    }, 500);
  });
}

fetchUserPromise(1)
  .then(user => console.log("User:", user))
  .catch(error => console.log("Error:", error.message));
```

**Chaining Promises — solving the Callback Hell problem from the previous chapter:**
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

getUser(1)
  .then(user => {
    console.log("User:", user);
    return getOrders(user.id); // Returning a Promise here chains it correctly!
  })
  .then(orders => {
    console.log("Orders:", orders);
    return getOrderDetails(orders[0]);
  })
  .then(details => {
    console.log("Details:", details);
  })
  .catch(error => {
    console.log("Error at any step:", error); // ONE centralized error handler for the whole chain!
  });
```
**Compare karo Module 13, chapter 02 ke nested-callback version se** — ye version **flat** hai (koi deep nesting nahi), aur error handling **ek jagah centralized** hai (`.catch()` chain mein kahi bhi hui error ko pakad leta hai) — Callback Hell ka clean solution.

**Critical rule — always `return` inside `.then()` when chaining:**
```js
// ⚠️ Common mistake — forgetting to return
getUser(1)
  .then(user => {
    getOrders(user.id); // ❌ Missing 'return'! The next .then() won't wait for this.
  })
  .then(orders => {
    console.log(orders); // undefined — because the previous .then() didn't return the promise!
  });

// ✅ Correct
getUser(1)
  .then(user => {
    return getOrders(user.id); // ✅ Returning ensures proper chaining
  })
  .then(orders => {
    console.log(orders); // Correctly waits and receives the orders array
  });
```

**Error propagation — errors skip to the nearest `.catch()`:**
```js
Promise.resolve("start")
  .then(value => {
    throw new Error("Something broke!");
  })
  .then(value => {
    console.log("This is SKIPPED"); // Never runs
  })
  .catch(error => {
    console.log("Caught:", error.message); // "Caught: Something broke!"
  })
  .then(() => {
    console.log("This DOES run — chain continues after catch!");
  });
```
Jaise hi ek error throw hoti hai (ya Promise reject hota hai) chain mein, saare beech ke `.then()` handlers **skip** ho jaate hain jab tak agla `.catch()` na mile — bilkul `try/catch` ke async equivalent jaisa.

## 8. Mental Model

Promise ko socho ek **"delivery tracking number"** — order place karte hi turant package nahi milta, lekin ek tracking number milta hai. Tum us number pe "jab deliver ho jaaye, mujhe batana" (`.then()`) ya "agar koi problem ho, mujhe batana" (`.catch()`) set kar sakte ho. Chaining ko socho ek **assembly line** — har station (`.then()`) apna kaam karta hai aur product ko agle station ko pass karta hai.

## 9. What Happens Internally?

Jab Promise create hoti hai, uska executor function **turant, synchronously** run hota hai (`resolve`/`reject` ko later call karne ke liye function references milte hain). `.then()` callbacks **microtask queue** mein jaate hain (Module 15 mein exact detail) — jo `setTimeout` (macrotask) se **higher priority** rakhta hai. Har `.then()` call ek **naya Promise object** return karta hai, jisse chaining possible hoti hai.

## 10. Common Mistakes

- `.then()` ke andar naye Promise ko `return` karna bhool jaana — chaining break ho jaati hai.
- `.catch()` ko missing chhodna — unhandled Promise rejection warnings/errors aate hain.
- Promise ko "immediately resolved value" jaisa treat karne ki koshish karna — Promise ki value hamesha `.then()` ke through hi access honi chahiye (ya `await` — Module 14).

## 11. Edge Cases

```js
const p = new Promise((resolve, reject) => {
  resolve("First");
  reject("Second"); // Ignored! A promise can only settle ONCE.
});
p.then(val => console.log(val)); // "First" — reject() call after resolve() has no effect
```

## 12. Real-World Usage

Almost saari modern async APIs Promises return karti hain — `fetch()` (Module 16), file operations (Module 18 mein `fs.promises`), database queries (Module 20). Promises async/await (Module 14) ka bhi foundation hain.

## 13. Comparison With Related Concepts

**⚠️ Legacy:** Callback-based async patterns (previous chapter).
**✅ Modern / Recommended:** Promises — flat chaining, centralized error handling, composable (Module 13, chapter 04 ke combinators ke saath).

## 14. Practice Questions

1. Promise ke 3 states kya hain?
2. `.then()` ke andar `return` bhool jaane se kya problem hoti hai?
3. Ek error `.catch()` tak kaise "propagate" hoti hai chain mein?

## 15. Challenge

Ek function `delay(ms)` likho jo ek Promise return kare jo `ms` milliseconds baad resolve ho. Isse use karke 3 sequential delays chain karo, har step pe ek message log karte hue.

## 16. Interview Questions

**Q1: Promise ke 3 states kya hain aur ek Promise apni state kitni baar change kar sakta hai?**
A: Promise ke 3 states hain: **Pending** (initial, operation in-progress), **Fulfilled** (successfully complete, value available), aur **Rejected** (failed, error/reason available). Ek Promise sirf **ek baar** settle ho sakta hai (pending se fulfilled ya rejected mein) — ek baar settle hone ke baad state **permanently fix** ho jaati hai, dobara change nahi ho sakti (jaise upar example mein `resolve()` ke baad `reject()` call karna ignore ho jaata hai).

**Q2: Promises Callback Hell ka problem kaise solve karti hain?**
A: Promises `.then()` chaining allow karti hain jo deeply nested callbacks ke bajaye **flat, sequential-looking code** banati hai — har `.then()` ek naya Promise return karta hai jispe agla `.then()` chain ho sakta hai. Error handling bhi **centralized** ho jaati hai — chain mein kahi bhi hui error automatically nearest `.catch()` tak propagate ho jaati hai, isliye har step pe manually error check karne ki zaroorat nahi padti (jaise callback-based code mein hoti thi).

## 17. Chapter Summary

Promises async operations ka eventual result represent karte hain — 3 states (pending, fulfilled, rejected) ke saath. `.then()`/`.catch()`/`.finally()` se flat, chainable, centralized-error-handling wala async code likha ja sakta hai, jo Callback Hell ka direct solution hai. `.then()` ke andar Promise return karna zaroori hai proper chaining ke liye.

## 18. Revision Checklist

- [ ] Promise ke 3 states aur unki permanence yaad hai.
- [ ] Promise chaining ka `return` rule clear hai.
- [ ] Error propagation mechanism (`.catch()` tak skip) samajh gaya.

---

**Next:** [04-Promise-Combinators.md](./04-Promise-Combinators.md)
