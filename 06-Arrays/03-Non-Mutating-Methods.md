# Non-Mutating Array Methods

**Module:** 06-Arrays
**Difficulty:** 🟡 Intermediate
**Previous:** [02-Mutating-Methods.md](./02-Mutating-Methods.md)
**Next:** [04-Iteration-and-Advanced-Methods.md](./04-Iteration-and-Advanced-Methods.md)

---

## 1. Learning Objectives

- Non-mutating methods samajhna: `slice`, `concat`, `map`, `filter`, `reduce`, `find`, `findIndex`, `some`, `every`, `includes`.
- Har method ka correct use-case identify karna.
- `map` vs `forEach` vs `filter` vs `reduce` ka difference clear karna.

## 2. Prerequisites

[02-Mutating-Methods.md](./02-Mutating-Methods.md)

## 3. Concept in Simple Hinglish

Non-mutating methods **original array ko chhedte nahi** — ye naya array/value return karte hain, original wahi rehta hai. Ye modern JavaScript (especially React jaise libraries) mein highly preferred hain kyunki inka behavior predictable aur safe hota hai.

## 4. Technical Explanation

| Method | Purpose | Returns |
|--------|---------|---------|
| `slice(start, end)` | Portion extract karta hai | New array |
| `concat(...arrays)` | Arrays combine karta hai | New array |
| `map(callback)` | Har element transform karta hai | New array (same length) |
| `filter(callback)` | Condition-matching elements | New array (subset) |
| `reduce(callback, initial)` | Single value mein "reduce" karta hai | Any value |
| `find(callback)` | First matching element | Element or `undefined` |
| `findIndex(callback)` | Index of first match | Number or `-1` |
| `some(callback)` | Kya koi element match karta hai | Boolean |
| `every(callback)` | Kya sab elements match karte hain | Boolean |
| `includes(value)` | Kya value array mein hai | Boolean |

## 5. Syntax

```js
arr.slice(start, end);
arr.map((item, index, array) => newItem);
arr.filter((item) => condition);
arr.reduce((accumulator, item) => newAccumulator, initialValue);
arr.find((item) => condition);
```

## 6. Basic Examples

```js
let numbers = [1, 2, 3, 4, 5];

console.log(numbers.map(n => n * 2));       // [2, 4, 6, 8, 10]
console.log(numbers.filter(n => n % 2 === 0)); // [2, 4]
console.log(numbers.reduce((sum, n) => sum + n, 0)); // 15
```

## 7. Detailed Examples

**`map()` — transforming every element:**
```js
let users = [{ name: "Amit" }, { name: "Priya" }];
let names = users.map(user => user.name);
console.log(names); // ["Amit", "Priya"]
```

**`filter()` — selecting matching elements:**
```js
let products = [
  { name: "Laptop", price: 50000 },
  { name: "Mouse", price: 500 },
  { name: "Keyboard", price: 1500 }
];

let affordable = products.filter(p => p.price < 2000);
console.log(affordable); // [{ name: "Mouse", ... }, { name: "Keyboard", ... }]
```

**`reduce()` — the most powerful and most confusing method:**
```js
let cart = [
  { item: "Book", price: 300, qty: 2 },
  { item: "Pen", price: 10, qty: 5 }
];

let total = cart.reduce((accumulator, current) => {
  return accumulator + current.price * current.qty;
}, 0); // 0 is the initial value of accumulator

console.log(total); // 300*2 + 10*5 = 650
```

**Step-by-step `reduce` trace:**
```
Initial: accumulator = 0
Step 1: accumulator = 0 + (300*2) = 600
Step 2: accumulator = 600 + (10*5) = 650
Final result: 650
```

**`find` vs `filter` — a common confusion:**
```js
let users2 = [{ id: 1, name: "A" }, { id: 2, name: "B" }, { id: 3, name: "C" }];

console.log(users2.find(u => u.id === 2));   // { id: 2, name: "B" } — single object
console.log(users2.filter(u => u.id === 2)); // [{ id: 2, name: "B" }] — array with one item
```
`find` **ek single element** (ya `undefined`) return karta hai — first match. `filter` **hamesha ek array** return karta hai — chahe 0, 1, ya multiple matches ho.

**`some()` vs `every()`:**
```js
let ages = [22, 17, 30, 15];

console.log(ages.some(age => age >= 18));  // true — at least one adult
console.log(ages.every(age => age >= 18)); // false — not everyone is an adult
```

**`slice()` vs `splice()` — a critical naming trap:**
```js
let arr = [1, 2, 3, 4, 5];

let sliced = arr.slice(1, 3);  // Non-mutating! Returns [2, 3]
console.log(arr);              // [1, 2, 3, 4, 5] — unchanged

// Compare with splice() from previous chapter — that one MUTATES!
```
`slice` aur `splice` naam mein similar hone ki wajah se **sabse common confusion** hai beginners ke liye — `slice` safe/non-mutating hai, `splice` mutating hai.

## 8. Mental Model

Non-mutating methods ko socho **photocopy machine** ki tarah — original document (array) untouched rehta hai, tumhe ek naya modified copy milta hai. `reduce()` ko socho ek **snowball** jo roll karte-karte (each element pe) bada hota jaata hai — final size (result) hi tumhe chahiye.

## 9. What Happens Internally?

Ye methods internally naya array (ya value) allocate karte hain aur callback function ke results ko usme collect karte hain, original array untouched chhod dete hain. `reduce` sirf ek accumulator variable maintain karta hai jo har iteration mein update hota hai — no array allocation required for reduce unless the result itself is a collection.

## 10. Common Mistakes

- `slice` aur `splice` ko confuse karna.
- `map()` ko sirf side-effects (jaise `console.log`) ke liye use karna jab `forEach` zyada semantically correct hota — `map` **naya array return karne ke liye** hai.
- `reduce()` mein initial value dena bhool jaana — empty array pe `reduce` bina initial value ke `TypeError` deta hai.
- `find()` se multiple matches expect karna — ye sirf **first** match deta hai.

## 11. Edge Cases

```js
console.log([].reduce((a, b) => a + b)); // ❌ TypeError: Reduce of empty array with no initial value
console.log([].reduce((a, b) => a + b, 0)); // ✅ 0 — safe with initial value
```

## 12. Real-World Usage

`map`/`filter`/`reduce` React/Redux jaisi libraries mein data-transformation ka backbone hain (list rendering, state derivations). `find` ek specific item ID se dhundne mein common hai. `some`/`every` validation logic mein (jaise "kya form ke saare fields filled hain?").

## 13. Comparison With Related Concepts

| Method | Returns | Use When |
|--------|---------|----------|
| `map` | New array, same length | Transform every element |
| `filter` | New array, subset | Select matching elements |
| `reduce` | Single accumulated value | Combine into one result (sum, object, etc.) |
| `forEach` | `undefined` | Just running side effects, no new array needed |
| `find` | Single element or `undefined` | Get the first match |

## 14. Practice Questions

1. `map` aur `filter` mein result array ki length ka kya guarantee hota hai?
2. `reduce()` mein initial value kyun important hai?
3. `slice` aur `splice` ka key difference kya hai?

## 15. Challenge

Ek array of orders `[{amount: 250, status: "paid"}, {amount: 100, status: "pending"}, {amount: 400, status: "paid"}]` diya hai. `filter` + `reduce` combine karke sirf "paid" orders ka total amount calculate karo.

## 16. Interview Questions

**Q1: `map()`, `filter()`, aur `reduce()` mein kya difference hai?**
A: `map()` har element ko transform karta hai aur **same length** ka naya array return karta hai. `filter()` condition ke basis pe elements select karta hai aur ek **subset** array return karta hai (length kam ya same ho sakti hai). `reduce()` poore array ko process karke ek **single accumulated value** (number, object, array, kuch bhi) return karta hai — jaise sum, average, ya grouped object banana. Teeno non-mutating hain — original array unchanged rehta hai.

**Q2: `find()` aur `filter()` mein kya difference hai?**
A: `find()` condition match karne wala **pehla single element** return karta hai (ya `undefined` agar kuch match na kare) — iteration match milte hi ruk jaati hai (early exit, efficient). `filter()` **saare matching elements ka array** return karta hai (empty array agar kuch match na kare) — poore array pe iterate karta hai chahe pehla match mil bhi jaaye.

## 17. Chapter Summary

`slice`, `concat`, `map`, `filter`, `reduce`, `find`, `findIndex`, `some`, `every`, `includes` — sab non-mutating hain, naya array/value return karte hain. `map`/`filter` transformation/selection ke liye hain, `reduce` complex aggregation ke liye, `find`/`some`/`every` search/validation ke liye.

## 18. Revision Checklist

- [ ] `map` vs `filter` vs `reduce` ka use-case clear hai.
- [ ] `find` vs `filter` ka return-type difference yaad hai.
- [ ] `slice` (safe) vs `splice` (mutating) confuse nahi hota ab.

---

**Next:** [04-Iteration-and-Advanced-Methods.md](./04-Iteration-and-Advanced-Methods.md)
