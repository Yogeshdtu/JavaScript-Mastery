# Iteration and Advanced Array Methods

**Module:** 06-Arrays
**Difficulty:** 🟡 Intermediate
**Previous:** [03-Non-Mutating-Methods.md](./03-Non-Mutating-Methods.md)
**Next:** [07-Objects/01-Object-Basics.md](../07-Objects/01-Object-Basics.md)

---

## 1. Learning Objectives

- `forEach()` se array iterate karna.
- `flat()` aur `flatMap()` se nested arrays handle karna.
- Arrays ko copy karne ke sahi (aur galat) tareeke revise karna.

## 2. Prerequisites

Module 06, chapters 01-03.

## 3. Concept in Simple Hinglish

`forEach()` array ke har element pe ek action perform karta hai (side effects ke liye) — `map()` jaisa lagta hai but naya array return nahi karta. `flat()` nested arrays ko "flatten" (seedha) kar deta hai, aur `flatMap()` map + flatten ek saath karta hai.

## 4. Technical Explanation

**`forEach(callback)`:** Har element pe callback call karta hai, `undefined` return karta hai. `break`/`continue` support nahi karta (unlike `for` loops).

**`flat(depth)`:** Nested arrays ko specified depth tak "flatten" karta hai. Default depth `1`.

**`flatMap(callback)`:** `map()` follow by `flat(1)` — but zyada efficient hai kyunki ek hi pass mein hota hai.

## 5. Syntax

```js
arr.forEach((item, index, array) => { /* side effect */ });
arr.flat(depth);
arr.flatMap(callback);
```

## 6. Basic Examples

```js
[1, 2, 3].forEach(n => console.log(n)); // 1 2 3 (printed, no return value used)

console.log([1, [2, 3], [4, [5, 6]]].flat());   // [1, 2, 3, 4, [5, 6]] (depth 1)
console.log([1, [2, 3], [4, [5, 6]]].flat(2));   // [1, 2, 3, 4, 5, 6] (depth 2)
console.log([1, [2, [3, [4]]]].flat(Infinity));  // [1, 2, 3, 4] (fully flat)
```

## 7. Detailed Examples

**`forEach()` vs `map()` — a crucial distinction:**
```js
let numbers = [1, 2, 3];

let result1 = numbers.forEach(n => n * 2);
console.log(result1); // undefined ⚠️ — forEach doesn't return anything useful

let result2 = numbers.map(n => n * 2);
console.log(result2); // [2, 4, 6] ✅ — map returns the transformed array
```
`forEach` ka use tab karo jab tumhe sirf **side effect** chahiye (jaise logging, DOM update, external variable modify karna) — naya array nahi banana. Agar naya transformed array chahiye, `map()` use karo.

**`flatMap()` — practical use case:**
```js
let sentences = ["Hello world", "How are you"];

// Want: individual words in a single flat array
let words = sentences.map(s => s.split(" "));
console.log(words); // [["Hello", "world"], ["How", "are", "you"]] — nested!

let wordsFlat = sentences.flatMap(s => s.split(" "));
console.log(wordsFlat); // ["Hello", "world", "How", "are", "you"] — flat!
```

**Copying arrays — all the ways, and their gotchas:**
```js
let original = [1, 2, { nested: "value" }];

// Method 1: Spread operator (shallow copy)
let copy1 = [...original];

// Method 2: slice() (shallow copy)
let copy2 = original.slice();

// Method 3: Array.from() (shallow copy)
let copy3 = Array.from(original);

// All are shallow — nested objects are still shared!
copy1[2].nested = "changed";
console.log(original[2].nested); // "changed" ⚠️ — nested object was shared

// For deep copy of arrays with nested objects:
let deepCopy = structuredClone(original);
```

**Iterating with index using `for...of` + `entries()`:**
```js
let items = ["a", "b", "c"];
for (const [index, value] of items.entries()) {
  console.log(index, value); // 0 a, 1 b, 2 c
}
```

## 8. Mental Model

`forEach()` ko socho ek **checklist jo tum manually har item pe kuch karte ho** (mark karna, print karna) — koi naya list nahi banti. `flat()` ko socho **matryoshka dolls ko khol ke sab dolls ko ek line mein rakh dena** — nesting khatam ho jaati hai.

## 9. What Happens Internally?

`forEach` internally ek simple loop jaisa hi hai — har index pe callback ko call karta hai, kuch collect nahi karta. `flat()` recursively (specified depth tak) nested arrays ko detect karke unke elements ko parent array mein "spread" kar deta hai — internally ye recursion + concatenation jaisa mechanism hai.

## 10. Common Mistakes

- `forEach()` ke return value ko use karne ki koshish karna — hamesha `undefined` hota hai.
- Deep-nested arrays ke liye `flat()` bina depth diye use karna — default depth sirf `1` hai, deeper nesting ke liye `flat(Infinity)` ya specific depth number chahiye.
- Array copy ko "complete/deep" samajhna jab wo actually shallow hai (nested objects still shared).

## 11. Edge Cases

```js
console.log([1, [2, [3, [4]]]].flat()); // [1, 2, [3, [4]]] — only flattens ONE level by default
```

## 12. Real-World Usage

`forEach` DOM updates aur logging jaisi side-effect-heavy operations mein common hai. `flat`/`flatMap` API responses ko normalize karne mein useful hai jab nested structures (jaise categories with sub-categories) ko single list mein convert karna ho.

## 13. Comparison With Related Concepts

| Method | Returns | Mutates Original? | Use Case |
|--------|---------|----------------------|----------|
| `forEach` | `undefined` | No | Side effects only |
| `map` | New array (same length) | No | Transform elements |
| `flat` | New flattened array | No | Un-nest arrays |
| `flatMap` | New flattened array | No | Map + flatten in one pass |

## 14. Practice Questions

1. `forEach()` ka return value kya hota hai?
2. `[1, [2, [3]]].flat()` ka result kya hoga (default depth)?
3. Array ko shallow copy karne ke 3 tareeke likho.

## 15. Challenge

Ek array of sentences diya hai: `["JS is fun", "Learn JS daily"]`. `flatMap()` use karke saare unique words nikaalo ek single flat array mein (duplicates allowed abhi, `Set` next module mein aayega unique karne ke liye).

## 16. Interview Questions

**Q1: `forEach()` aur `map()` mein kya difference hai, aur kab kaunsa use karna chahiye?**
A: `forEach()` har element pe callback execute karta hai lekin **`undefined` return karta hai** — ye purely side-effects (logging, external state update) ke liye hai. `map()` har element ko transform karta hai aur **naya array return karta hai** (same length) — ye tab use hota hai jab transformed data ki zaroorat ho. Rule of thumb: agar result ko store/use karna hai, `map()`; agar sirf action perform karna hai, `forEach()`.

**Q2: Array ko copy karne ke tareeke (spread, `slice()`, `Array.from()`) "shallow copy" kyun kehlate hain?**
A: Ye methods sirf **top-level elements** ka naya array banate hain. Agar array ke andar objects/arrays (nested reference types) hain, unke references **same rehte hain** dono (original aur copy) mein — matlab agar copy mein nested object mutate karo, original mein bhi change dikhega. Poori tarah independent copy (nested levels tak) ke liye deep copy chahiye — `structuredClone()` ya recursive cloning.

## 17. Chapter Summary

`forEach()` side-effects ke liye hai, `undefined` return karta hai. `flat()`/`flatMap()` nested arrays ko flatten karte hain — `flat()` default depth 1 hai, deeper nesting ke liye explicit depth ya `Infinity` chahiye. Array copying (spread, `slice`, `Array.from`) shallow hoti hai — nested objects share hote hain.

## 18. Revision Checklist

- [ ] `forEach` vs `map` ka difference clear hai.
- [ ] `flat()`/`flatMap()` ka default-depth behavior yaad hai.
- [ ] Shallow vs deep array copy ka difference samajh gaya.

---

**Module 06 Complete!** Next Module: [07-Objects/01-Object-Basics.md](../07-Objects/01-Object-Basics.md)
