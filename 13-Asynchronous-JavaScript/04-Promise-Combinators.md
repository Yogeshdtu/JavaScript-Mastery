# Promise Combinators — all, allSettled, race, any

**Module:** 13-Asynchronous-JavaScript
**Difficulty:** 🔴 Advanced
**Previous:** [03-Promises-Deep-Dive.md](./03-Promises-Deep-Dive.md)
**Next:** [14-Promises-and-Async-Await/01-Async-Await-Basics.md](../14-Promises-and-Async-Await/01-Async-Await-Basics.md)

---

## 1. Learning Objectives

- `Promise.all()`, `Promise.allSettled()`, `Promise.race()`, `Promise.any()` ka exact behavior samajhna.
- Har combinator ka sahi real-world use-case identify karna.
- Multiple parallel Promises manage karna.

## 2. Prerequisites

Module 13, chapter 03.

## 3. Concept in Simple Hinglish

Kabhi-kabhi humein **multiple async operations parallel mein** chalani hoti hain aur unke results ko ek saath handle karna hota hai — jaise 3 alag APIs se data fetch karna aur sabka wait karna. JavaScript 4 "combinator" methods deta hai jo different scenarios ke liye multiple Promises ko combine karte hain.

## 4. Technical Explanation

| Method | Resolves When | Rejects When |
|--------|-----------------|-------------------|
| `Promise.all()` | ALL promises fulfill | ANY promise rejects (fails fast) |
| `Promise.allSettled()` | ALL promises settle (fulfill or reject) | Never rejects |
| `Promise.race()` | FIRST promise settles (fulfill or reject) | If that first one is a rejection |
| `Promise.any()` | FIRST promise fulfills | Only if ALL promises reject |

## 5. Syntax

```js
Promise.all([p1, p2, p3]);
Promise.allSettled([p1, p2, p3]);
Promise.race([p1, p2, p3]);
Promise.any([p1, p2, p3]);
```

## 6. Basic Examples

```js
const p1 = Promise.resolve(1);
const p2 = Promise.resolve(2);
const p3 = Promise.resolve(3);

Promise.all([p1, p2, p3]).then(results => console.log(results)); // [1, 2, 3]
```

## 7. Detailed Examples

**`Promise.all()` — fail-fast behavior:**
```js
function delay(value, ms, shouldFail = false) {
  return new Promise((resolve, reject) => {
    setTimeout(() => shouldFail ? reject(`Failed: ${value}`) : resolve(value), ms);
  });
}

Promise.all([
  delay("A", 100),
  delay("B", 200),
  delay("C", 300)
]).then(results => console.log(results)); // ["A", "B", "C"] — after ~300ms (waits for the slowest)

Promise.all([
  delay("A", 100),
  delay("B", 200, true), // This one fails!
  delay("C", 300)
]).then(results => console.log(results))
  .catch(error => console.log(error)); // "Failed: B" — rejects immediately when ANY promise fails, doesn't wait for C!
```
`Promise.all()` **"all or nothing"** hai — agar koi ek bhi Promise reject hota hai, poora `Promise.all()` turant reject ho jaata hai (baaki Promises ka result discard ho jaata hai, chahe wo baad mein successfully resolve bhi ho jaayen).

**`Promise.allSettled()` — get every result, regardless of success/failure:**
```js
Promise.allSettled([
  delay("A", 100),
  delay("B", 200, true),
  delay("C", 300)
]).then(results => console.log(results));
/*
[
  { status: "fulfilled", value: "A" },
  { status: "rejected", reason: "Failed: B" },
  { status: "fulfilled", value: "C" }
]
*/
```
`allSettled()` **kabhi reject nahi hota** — ye har Promise ka final status (fulfilled/rejected) collect karke ek array return karta hai. Use karo jab tumhe **sabhi results chahiye**, chahe kuch fail bhi ho jaayen (jaise multiple independent API calls jinme se kuch fail hona acceptable hai).

**`Promise.race()` — whichever settles first, wins:**
```js
Promise.race([
  delay("Slow", 500),
  delay("Fast", 100)
]).then(result => console.log(result)); // "Fast" — settles first (100ms < 500ms)
```
**Real-world use-case — timeout pattern:**
```js
function fetchWithTimeout(promise, timeoutMs) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject(new Error("Request timed out")), timeoutMs)
  );
  return Promise.race([promise, timeout]);
}

fetchWithTimeout(delay("Data", 5000), 2000)
  .then(result => console.log(result))
  .catch(error => console.log(error.message)); // "Request timed out" — because 2000ms < 5000ms
```
Ye pattern real applications mein bahut useful hai — agar ek API call bahut slow ho jaaye, user ko forever wait nahi karana, ek timeout error dikhana.

**`Promise.any()` — first SUCCESS wins, ignores failures until all fail:**
```js
Promise.any([
  delay("A", 100, true), // fails
  delay("B", 200, true), // fails
  delay("C", 300)          // succeeds
]).then(result => console.log(result)); // "C" — the first (and only) success

Promise.any([
  delay("A", 100, true),
  delay("B", 200, true)
]).catch(error => console.log(error)); // AggregateError — ALL promises rejected
```
`Promise.any()` sirf tab reject hota hai jab **sab** promises fail ho jaate hain — ye "give me the fastest successful result, ignore failures" scenario ke liye perfect hai (jaise multiple mirror servers se data fetch karna, jo bhi pehle successfully respond kare).

## 8. Mental Model

`Promise.all()` ko socho ek **group project** — sabko apna part submit karna hai, agar ek bhi fail hota hai, poora project "incomplete" mana jaata hai. `allSettled()` ek **report card** hai — har subject (Promise) ka result dikhata hai, pass ho ya fail. `race()` ek **race jaisa hi hai** — jo pehle finish line cross kare (settle ho, chahe jeet ke ya haar ke), wahi result count hota hai. `any()` ek **"kisi ek ko toh jeetna chahiye"** competition hai — sirf pehli jeet (success) matter karti hai.

## 9. What Happens Internally?

Ye sabhi methods internally saari di gayi Promises ko **parallel mein start** kar dete hain (sequential nahi — sabhi turant "fire" ho jaati hain), aur phir specific combinator logic ke hisaab se apne khud ke ek naye Promise ko resolve/reject karte hain based on individual Promises ke settling patterns.

## 10. Common Mistakes

- `Promise.all()` use karna jab actually `allSettled()` chahiye tha (jab partial failures acceptable hon).
- `Promise.race()` ko sirf "fastest success" ke liye use karna — actually ye fastest **settlement** (success ya failure) return karta hai, `any()` specifically success ke liye hai.
- Sequential operations (jo ek dusre pe depend karte hain) ke liye combinators use karne ki koshish karna — ye sab **independent, parallel** operations ke liye hain.

## 11. Edge Cases

```js
Promise.all([]).then(results => console.log(results)); // [] — resolves immediately with empty array
Promise.race([]).then(() => {}); // Never settles! An empty array means nothing to race.
```

## 12. Real-World Usage

`Promise.all()` multiple independent API calls parallel mein fetch karne ke liye jab sabki zaroorat ho (jaise dashboard ke multiple widgets ka data). `allSettled()` bulk operations mein (jaise multiple files upload karna, kuch fail ho sakte hain). `race()` timeout patterns ke liye. `any()` multiple fallback sources se fastest response chahiye ho tab.

## 13. Comparison With Related Concepts

| Scenario | Best Combinator |
|----------|---------------------|
| Need ALL results, fail if any fails | `Promise.all()` |
| Need ALL results, regardless of failures | `Promise.allSettled()` |
| Need the fastest result (success or failure) | `Promise.race()` |
| Need the fastest SUCCESSFUL result | `Promise.any()` |

## 14. Practice Questions

1. `Promise.all()` aur `Promise.allSettled()` mein failure-handling ka difference kya hai?
2. `Promise.race()` ka ek practical real-world use-case batao.
3. `Promise.any()` kab reject hota hai?

## 15. Challenge

3 mock API functions banao (`delay()` helper use karke) jinme se ek fail ho. In teeno ko `Promise.all()`, `Promise.allSettled()`, aur `Promise.any()` se test karo aur results compare karo.

## 16. Interview Questions

**Q1: `Promise.all()` aur `Promise.allSettled()` mein kya difference hai?**
A: `Promise.all()` **"fail-fast"** hai — agar array mein se koi ek bhi Promise reject hoti hai, poora `Promise.all()` turant reject ho jaata hai, baaki promises ka result ignore ho jaata hai (chahe wo successfully resolve bhi ho jaayen baad mein). `Promise.allSettled()` **kabhi reject nahi hota** — ye har Promise ka final outcome (`{status: "fulfilled", value}` ya `{status: "rejected", reason}`) collect karke array return karta hai, chahe kuch/sab fail ho jaayen. Use `all()` jab sabka success zaroori ho; use `allSettled()` jab partial failures acceptable hon aur sabke results dekhne hon.

**Q2: `Promise.race()` aur `Promise.any()` mein kya difference hai?**
A: `Promise.race()` us Promise ke result se resolve/reject hota hai jo **sabse pehle settle** (fulfill YA reject) hota hai — agar fastest Promise fail hoti hai, `race()` bhi reject ho jaata hai. `Promise.any()` sirf **successful (fulfilled)** results mein interested hai — ye us Promise se resolve hota hai jo **sabse pehle successfully fulfill** hoti hai, failures ko ignore karta hai jab tak koi success na mile; sirf tab reject hota hai jab **sabhi** promises fail ho jaayen (with an `AggregateError`).

## 17. Chapter Summary

`Promise.all()` sabka success chahiye (fail-fast). `Promise.allSettled()` sabke results chahiye, failures ke saath bhi. `Promise.race()` fastest settlement (success/failure) return karta hai — timeout patterns ke liye useful. `Promise.any()` fastest success return karta hai, failures ko sirf tab surface karta hai jab sab fail ho jaayen.

## 18. Revision Checklist

- [ ] Chaaro combinators ka resolve/reject-trigger-condition yaad hai.
- [ ] `all()` vs `allSettled()` ka use-case-based choice clear hai.
- [ ] `race()` ka timeout-pattern use-case samajh gaya.

---

**Module 13 Complete!** Next Module: [14-Promises-and-Async-Await/01-Async-Await-Basics.md](../14-Promises-and-Async-Await/01-Async-Await-Basics.md)
