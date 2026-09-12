# for...of and for...in

**Module:** 04-Control-Flow
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [02-Loops-for-while-dowhile.md](./02-Loops-for-while-dowhile.md)
**Next:** [04-break-and-continue.md](./04-break-and-continue.md)

---

## 1. Learning Objectives

- `for...of` se iterable values (arrays, strings, Maps, Sets) pe loop karna.
- `for...in` se object properties (keys) pe loop karna.
- Dono ke beech confusion clear karna — kab kaunsa use karna hai.

## 2. Prerequisites

Module 04, chapters 01-02.

## 3. Concept in Simple Hinglish

`for...of` **values** pe loop karta hai — arrays, strings, jaisi cheezo ke individual elements milte hain. `for...in` **keys/property-names** pe loop karta hai — objects ki properties ke naam milte hain. Naam similar hone ki wajah se beginners confuse ho jaate hain — yaad rakhne ka tareeka: "of" = values **of** the collection, "in" = keys **in** the object.

## 4. Technical Explanation

**`for...of`** — works on **iterables** (Array, String, Map, Set, generators). Values directly milte hain.

**`for...in`** — works on **objects** (aur arrays bhi, technically, but not recommended). Property **keys** (as strings) milte hain, including inherited enumerable properties.

## 5. Syntax

```js
for (const value of iterable) {
  // use value
}

for (const key in object) {
  // use key
}
```

## 6. Basic Examples

```js
const fruits = ["apple", "banana", "cherry"];

for (const fruit of fruits) {
  console.log(fruit); // apple, banana, cherry (VALUES)
}

const user = { name: "Ravi", age: 30 };

for (const key in user) {
  console.log(key); // "name", "age" (KEYS)
}
```

## 7. Detailed Examples

**`for...in` on arrays — why it's discouraged:**
```js
const arr = [10, 20, 30];
arr.customProp = "extra"; // Custom property added to array

for (const index in arr) {
  console.log(index); // "0", "1", "2", "customProp" ⚠️
}
```
`for...in` array ke custom (non-index) properties ko bhi iterate kar leta hai, jo unexpected hai. Isi liye arrays ke liye **`for...of`** ya `.forEach()` use karna chahiye, `for...in` nahi.

**`for...of` with entries — index + value dono chahiye ho to:**
```js
const colors = ["red", "green", "blue"];

for (const [index, color] of colors.entries()) {
  console.log(index, color); // 0 "red", 1 "green", 2 "blue"
}
```

**`for...in` with objects — combining with values:**
```js
const product = { name: "Phone", price: 15000, inStock: true };

for (const key in product) {
  console.log(`${key}: ${product[key]}`);
}
// name: Phone
// price: 15000
// inStock: true
```

**Getting object entries as key-value pairs (modern alternative):**
```js
for (const [key, value] of Object.entries(product)) {
  console.log(`${key}: ${value}`);
}
```
`Object.entries()` object ko `[key, value]` pairs ke array mein convert kar deta hai, jise `for...of` se iterate kiya ja sakta hai — ye modern, recommended approach hai object iteration ke liye.

## 8. Mental Model

`for...of` ko socho ek **fruit basket se ek-ek fruit nikaalna** — tumhe seedha fruit (value) milta hai. `for...in` ko socho ek **filing cabinet ke drawers ke labels padhna** — tumhe label (key) milta hai, andar ki cheez dekhne ke liye tumhe drawer khud kholna padta hai (`obj[key]`).

## 9. What Happens Internally?

`for...of` **iterator protocol** use karta hai — ye value ke `Symbol.iterator` method ko call karta hai, jo har call pe `{value, done}` return karta hai jab tak `done: true` na ho. `for...in` object ki **enumerable properties** ko prototype chain ke through traverse karta hai — ye Symbol.iterator use nahi karta, isliye plain objects (jo default iterable nahi hote) pe bhi kaam karta hai.

## 10. Common Mistakes

- Arrays pe `for...in` use karna — inherited/custom properties bhi iterate ho sakti hain, aur order guarantee nahi hoti technically.
- Plain objects pe `for...of` directly use karne ki koshish karna — objects by default iterable nahi hote, `TypeError` aayega (`Object.entries()`/`Object.keys()`/`Object.values()` use karo).
- `for...in` mein `hasOwnProperty()` check na karna jab inherited properties avoid karni ho.

## 11. Edge Cases

```js
// for...of directly on plain object — ERROR
const obj = { a: 1 };
// for (const val of obj) {} // ❌ TypeError: obj is not iterable

// Correct approaches:
for (const val of Object.values(obj)) { console.log(val); } // 1
```

## 12. Real-World Usage

`for...of` bahut common hai arrays, Maps, Sets iterate karne ke liye jab `break`/`continue` chahiye ho (jo `.forEach()` support nahi karta). `for...in` object ki properties dynamically inspect karne mein use hota hai, jaise debugging ya generic utility functions banane mein.

## 13. Comparison With Related Concepts

| Aspect | for...of | for...in |
|--------|----------|----------|
| Iterates over | Values | Keys (property names) |
| Works on | Iterables (Array, String, Map, Set) | Objects (any enumerable properties) |
| Includes inherited props? | N/A | Yes (unless checked) |
| Recommended for arrays? | ✅ Yes | ⚠️ No |
| Recommended for objects? | ⚠️ No (not iterable by default) | ✅ Yes |

## 14. Practice Questions

1. `for...of` aur `for...in` mein se kaunsa values return karta hai aur kaunsa keys?
2. Arrays ke liye `for...in` kyun avoid karna chahiye?
3. Plain object ko `for...of` se iterate karne ka sahi tareeka kya hai?

## 15. Challenge

Ek object `scores = { math: 90, science: 85, english: 78 }` banao. `for...in` use karke total aur average score calculate karo. Phir `Object.entries()` + `for...of` se same calculation dobara likho.

## 16. Interview Questions

**Q1: `for...of` aur `for...in` mein kya difference hai?**
A: `for...of` iterable objects (arrays, strings, Maps, Sets) ke **values** pe iterate karta hai — ye iterator protocol use karta hai. `for...in` kisi bhi object ki **enumerable property keys** (including inherited ones) pe iterate karta hai — ye prototype chain traverse karta hai. Arrays ke liye `for...of` recommended hai, objects ke liye `for...in` (ya better, `Object.entries()` ke saath `for...of`).

**Q2: Arrays pe `for...in` use karna kyun problematic ho sakta hai?**
A: `for...in` array ki sirf numeric indices tak limited nahi hai — agar array mein koi custom property add ki gayi hai (jaise `arr.customProp = "x"`), ya prototype chain mein enumerable properties hain, to `for...in` unhe bhi iterate kar leta hai, jo unexpected results de sakta hai. Iska koi guaranteed numeric order bhi nahi hai specification ke hisaab se (though practically browsers order maintain karte hain). Isi liye arrays ke liye `for...of` ya array methods (`forEach`, `map`) safer hain.

## 17. Chapter Summary

`for...of` iterable values pe loop karta hai (arrays, strings, etc.) aur arrays ke liye recommended hai. `for...in` object property keys pe loop karta hai (including inherited), objects ke liye useful hai. Plain objects `for...of` ke saath directly kaam nahi karte — `Object.entries/keys/values` ke saath combine karna padta hai.

## 18. Revision Checklist

- [ ] `for...of` values return karta hai, ye yaad hai.
- [ ] `for...in` keys return karta hai aur arrays ke liye risky hai, ye clear hai.
- [ ] `Object.entries()` ke saath `for...of` combine karna aata hai.

---

**Next:** [04-break-and-continue.md](./04-break-and-continue.md)
