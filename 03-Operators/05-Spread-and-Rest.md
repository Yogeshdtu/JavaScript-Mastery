# Spread and Rest Operators

**Module:** 03-Operators
**Difficulty:** 🟡 Intermediate
**Previous:** [04-Ternary-Unary-Bitwise.md](./04-Ternary-Unary-Bitwise.md)
**Next:** [06-typeof-instanceof-in-delete.md](./06-typeof-instanceof-in-delete.md)

---

## 1. Learning Objectives

- Spread operator (`...`) se arrays/objects expand karna seekhna.
- Rest operator (`...`) se multiple arguments collect karna seekhna.
- Spread aur Rest ke syntactically same hone ke bawajood alag purpose samajhna.

## 2. Prerequisites

Module 03, chapters 01-04.

## 3. Concept in Simple Hinglish

Spread aur Rest dono `...` (three dots) use karte hain, lekin opposite kaam karte hain. **Spread** ek array/object ko **"khol ke fail deta hai"** (expand karta hai) individual elements mein. **Rest** multiple individual values ko **"ikattha karke ek array mein pack kar deta hai"**.

## 4. Technical Explanation

**Spread (`...`)** — existing iterable (array, string, object) ko expand karta hai:
- Arrays ko combine/copy karna.
- Function calls mein arguments individually pass karna.
- Objects ko merge/copy karna.

**Rest (`...`)** — function parameters mein ya destructuring mein "remaining" values ko array mein collect karta hai. Hamesha **last parameter** honi chahiye.

## 5. Syntax

```js
// Spread
let newArr = [...arr1, ...arr2];
let newObj = { ...obj1, ...obj2 };
myFunction(...argsArray);

// Rest
function myFunc(...args) { }
let [first, ...rest] = [1, 2, 3, 4];
```

## 6. Basic Examples

```js
// Spread example
let nums = [1, 2, 3];
let moreNums = [...nums, 4, 5];
console.log(moreNums); // [1, 2, 3, 4, 5]

// Rest example
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
console.log(sum(1, 2, 3, 4)); // 10
```

## 7. Detailed Examples

**Spread — Array copying aur merging:**
```js
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];

let combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

let copy = [...arr1]; // Shallow copy (naya array, but nested objects still shared)
copy.push(4);
console.log(arr1); // [1, 2, 3] — original unaffected
console.log(copy);  // [1, 2, 3, 4]
```

**Spread — Object merging (later keys override earlier):**
```js
let defaults = { theme: "light", fontSize: 14 };
let userPrefs = { fontSize: 18 };

let finalSettings = { ...defaults, ...userPrefs };
console.log(finalSettings); // { theme: "light", fontSize: 18 }
```
Object spread mein, agar same key dono objects mein hai, **baad wali (right-side) value jeet jaati hai** — isi liye `userPrefs` ka `fontSize` override kar gaya.

**Rest — Function parameters mein flexibility:**
```js
function logAll(firstArg, ...remainingArgs) {
  console.log("First:", firstArg);
  console.log("Rest:", remainingArgs);
}

logAll(1, 2, 3, 4);
// First: 1
// Rest: [2, 3, 4]
```

**Rest — Destructuring mein:**
```js
let [winner, ...otherPlayers] = ["Alice", "Bob", "Charlie", "Dave"];
console.log(winner);       // "Alice"
console.log(otherPlayers); // ["Bob", "Charlie", "Dave"]

let { id, ...otherDetails } = { id: 1, name: "Product A", price: 500 };
console.log(id);            // 1
console.log(otherDetails);  // { name: "Product A", price: 500 }
```

## 8. Mental Model

Spread ko socho ek **suitcase khol ke saara saaman table pe faila dena** — sab individual items ban jaate hain. Rest ko socho **saara bacha hua saaman ek naye suitcase mein pack karna** — collect karke ek jagah rakhna. Same `...` symbol, opposite direction.

## 9. What Happens Internally?

Spread iterable protocol use karta hai — jo bhi value `Symbol.iterator` implement karti hai (arrays, strings, Maps, Sets), usse spread kiya ja sakta hai (except objects — objects ka spread special object-spread syntax hai, iterable protocol nahi). Rest parameters function call ke time bache hue arguments ko engine internally ek naya array bana ke collect karta hai.

## 10. Common Mistakes

- Rest parameter ko last position pe na rakhna — `function f(...rest, last)` **SyntaxError** dega.
- Spread ko deep copy samajhna — ye sirf shallow copy karta hai.
- `arguments` object (old way) aur rest parameters (`...args`, modern way) ko confuse karna — rest parameters actual array hote hain, `arguments` array-like object hai jispe array methods directly nahi chalte.

## 11. Edge Cases

```js
function example(a, b, ...rest) {
  console.log(rest); // Always an array, even if empty
}
example(1, 2); // rest = []

let str = "hello";
console.log([...str]); // ['h', 'e', 'l', 'l', 'o'] — strings are iterable!
```

## 12. Real-World Usage

Spread bahut common hai React mein immutable state updates ke liye (`setState({...prevState, key: newValue})`), aur function calls mein array ko arguments ki tarah pass karne ke liye (`Math.max(...numbers)`). Rest parameters variable-argument functions banane ke liye (jaise logging utilities, math helper functions) use hote hain.

## 13. Comparison With Related Concepts

| Aspect | Spread | Rest |
|--------|--------|------|
| Direction | Expands (unpacks) | Collects (packs) |
| Used In | Array/object literals, function calls | Function parameters, destructuring |
| Position | Anywhere | Must be last |

## 14. Practice Questions

1. `[...{a: 1, b: 2}]` — kya ye valid hai? Kyun?
2. `function f(a, ...b, c) {}` — ye valid hai ya nahi?
3. Rest parameter aur `arguments` object mein kya difference hai?

## 15. Challenge

Ek function `mergeSettings(base, ...overrides)` likho jo ek base object ko multiple override objects ke saath merge kare, spread operator use karke, aur final merged object return kare.

## 16. Interview Questions

**Q1: Spread aur Rest operators mein syntax same (`...`) hone ke bawajood kya difference hai?**
A: Spread operator ek existing array/object/iterable ko individual elements mein "expand" karta hai — array literals, object literals, ya function calls mein use hota hai (`[...arr]`, `myFn(...args)`). Rest operator opposite kaam karta hai — multiple individual values ko ek array mein "collect" karta hai — function parameters ya destructuring mein use hota hai (`function f(...args)`, `let [a, ...rest] = arr`). Context (kaha use ho raha hai) hi decide karta hai kaunsa behavior apply hoga.

**Q2: Object spread mein agar duplicate keys hon to kya hota hai?**
A: Jab multiple objects spread karke merge kiye jaate hain aur same key multiple objects mein exist karti hai, to **jo object baad mein (right side) spread hota hai uski value jeet jaati hai** — later properties earlier ones ko override kar deti hain. Ye pattern default settings ko user-specific overrides ke saath merge karne mein bahut common hai.

## 17. Chapter Summary

Spread operator arrays/objects ko expand karta hai — copying, merging, aur function calls mein use hota hai. Rest operator multiple values ko array mein collect karta hai — flexible function parameters aur destructuring mein use hota hai. Dono same syntax share karte hain lekin context unka behavior determine karta hai.

## 18. Revision Checklist

- [ ] Spread ka array/object copy-merge use-case clear hai.
- [ ] Rest parameters function mein kaise use hote hain, yaad hai.
- [ ] Spread shallow copy karta hai, ye samajh gaya.

---

**Next:** [06-typeof-instanceof-in-delete.md](./06-typeof-instanceof-in-delete.md)
