# Destructuring and Spread (Objects)

**Module:** 07-Objects
**Difficulty:** 🟡 Intermediate
**Previous:** [01-Object-Basics.md](./01-Object-Basics.md)
**Next:** [03-Object-Methods.md](./03-Object-Methods.md)

---

## 1. Learning Objectives

- Object destructuring se properties easily extract karna.
- Nested destructuring aur default values use karna.
- Renaming variables destructuring ke time.
- Object spread revise karna object-specific context mein.

## 2. Prerequisites

Module 07, chapter 01. [03-Operators/05-Spread-and-Rest.md](../03-Operators/05-Spread-and-Rest.md) helpful revision hoga.

## 3. Concept in Simple Hinglish

Destructuring ek shortcut hai jisse object ki properties ko **directly variables mein "nikaal"** sakte ho, bina baar-baar `obj.property` likhe. Ye code ko concise aur readable banata hai, especially jab function parameters mein object properties use karni ho.

## 4. Technical Explanation

```js
const { key1, key2 } = object;
```
Ye `key1` aur `key2` naam ki variables banata hai jinki values `object.key1` aur `object.key2` hoti hain.

## 5. Syntax

```js
const { a, b } = obj;
const { a: renamedA } = obj;
const { a = defaultValue } = obj;
const { nested: { inner } } = obj;
function fn({ a, b }) { }
```

## 6. Basic Examples

```js
const user = { name: "Vikram", age: 32 };
const { name, age } = user;
console.log(name, age); // "Vikram" 32
```

## 7. Detailed Examples

**Renaming variables during destructuring:**
```js
const product = { id: 101, title: "Headphones" };
const { title: productName } = product;
console.log(productName); // "Headphones"
// console.log(title); // ❌ ReferenceError — 'title' itself doesn't exist as a variable
```

**Default values in destructuring:**
```js
const settings = { theme: "dark" };
const { theme, fontSize = 14 } = settings;
console.log(theme, fontSize); // "dark" 14 (fontSize wasn't in object, so default used)
```

**Nested destructuring:**
```js
const order = {
  id: 1,
  customer: {
    name: "Ishaan",
    address: { city: "Chennai", pincode: "600001" }
  }
};

const { customer: { name, address: { city } } } = order;
console.log(name, city); // "Ishaan" "Chennai"
```

**Destructuring in function parameters — extremely common pattern:**
```js
function displayUser({ name, age = 18 }) {
  console.log(`${name} is ${age} years old`);
}

displayUser({ name: "Tara" });          // "Tara is 18 years old"
displayUser({ name: "Zoya", age: 25 }); // "Zoya is 25 years old"
```
Ye pattern configuration-object arguments ke saath bahut common hai — function signature khud se documents karti hai kaunse properties expected hain.

**Combining destructuring with rest operator:**
```js
const { id, ...otherDetails } = { id: 1, name: "Item A", price: 500, inStock: true };
console.log(id);            // 1
console.log(otherDetails);  // { name: "Item A", price: 500, inStock: true }
```

**Object spread — merging with override:**
```js
const defaultConfig = { theme: "light", showSidebar: true, fontSize: 14 };
const userConfig = { theme: "dark" };

const finalConfig = { ...defaultConfig, ...userConfig };
console.log(finalConfig); // { theme: "dark", showSidebar: true, fontSize: 14 }
```

## 8. Mental Model

Destructuring ko socho ek **parcel unpack karna** — box (object) ke andar se specific items (properties) nikaal ke direct apne haath mein (variables mein) le lena, poora box carry karne ke bajaye.

## 9. What Happens Internally?

Engine destructuring pattern ko dekh kar source object se matching keys ki values ko lookup karta hai aur naye local variables mein assign karta hai — ye syntactic sugar hai equivalent multiple `const x = obj.x;` statements ka, ek line mein.

## 10. Common Mistakes

- Destructuring karte time key ka naam galat likhna — silently `undefined` milta hai, error nahi aata.
- Nested destructuring mein galat structure assume karna jab actual data mein wo nested property exist nahi karti (agar beech ka level `undefined`/`null` hai, `TypeError` aayega — Module 03, chapter 03 ka optional chaining yahan combine kiya ja sakta hai).
- Renamed variable ke original key naam se access karne ki koshish karna.

## 11. Edge Cases

```js
const { missing } = {}; // No error — missing is just undefined
console.log(missing); // undefined

const { deeply: { nested } } = {}; // ❌ TypeError: Cannot destructure property 'nested' of 'undefined'
```
Nested destructuring tab error deti hai jab beech ka level `undefined` hota hai — agar ye possible hai, default value ya optional chaining ke saath handle karo.

## 12. Real-World Usage

React mein props destructuring (`function Component({ title, onClick })`) extremely common hai. API responses se specific fields extract karna, module imports (`const { useState } = require("react")`) sab destructuring ka use hai.

## 13. Comparison With Related Concepts

| Concept | Purpose |
|---------|---------|
| Destructuring | Extract values from objects/arrays into variables |
| Spread (objects) | Expand/merge object properties into a new object |
| Rest (in destructuring) | Collect "remaining" properties into a new object |

## 14. Practice Questions

1. Destructuring mein default value kab apply hoti hai?
2. Nested destructuring likho ek 2-level-deep object ke liye.
3. Object spread mein duplicate keys ka resolution kaise hota hai?

## 15. Challenge

Ek function `formatAddress` likho jo destructuring parameters use kare: `formatAddress({ street, city, state = "Unknown", pincode })` aur ek formatted string return kare `"street, city, state - pincode"`.

## 16. Interview Questions

**Q1: Object destructuring mein default values kaise kaam karti hain?**
A: Destructuring pattern mein `{ key = defaultValue }` likhne se, agar source object mein `key` `undefined` hai (ya exist nahi karti), to `defaultValue` use hoti hai. Important: sirf `undefined` default trigger karta hai — agar property explicitly `null` ya koi aur value hai, default use nahi hoga (jaise default function parameters, Module 05 chapter 03 mein dekha tha).

**Q2: Destructuring aur spread operator mein kya relationship hai?**
A: Dono object properties ke saath kaam karte hain lekin opposite direction mein — destructuring object se **specific properties nikaal ke** individual variables banata hai, jabki spread ek object ko **expand karke** naya object banane ke liye use hota hai (merging/copying). Dono ko combine bhi kiya ja sakta hai — jaise destructuring mein rest operator (`const {a, ...rest} = obj`) "remaining" properties ko naye object mein collect karta hai.

## 17. Chapter Summary

Object destructuring properties ko directly variables mein extract karta hai — renaming, default values, aur nested patterns support karta hai. Function parameters mein destructuring bahut common hai. Object spread properties ko merge/copy karta hai, duplicate keys mein later value jeet jaati hai.

## 18. Revision Checklist

- [ ] Basic aur nested destructuring likh sakta hoon.
- [ ] Renaming aur default values ka syntax yaad hai.
- [ ] Function parameter destructuring ka real-world pattern samajh gaya.

---

**Next:** [03-Object-Methods.md](./03-Object-Methods.md)
