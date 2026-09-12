# Map, Set, WeakMap, WeakSet

**Module:** 10-Modern-JavaScript
**Difficulty:** 🟡 Intermediate
**Previous:** [03-Prototypes-Deep-Dive.md](./03-Prototypes-Deep-Dive.md)
**Next:** [05-Iterators-and-Generators.md](./05-Iterators-and-Generators.md)

---

## 1. Learning Objectives

- `Map` se key-value pairs (any key type) manage karna.
- `Set` se unique values collection banana.
- `WeakMap`/`WeakSet` ka memory-related use-case samajhna.

## 2. Prerequisites

Modules 06-07 (Arrays, Objects).

## 3. Concept in Simple Hinglish

**`Map`** object jaisa hai but keys **kisi bhi type** ki ho sakti hain (sirf string nahi). **`Set`** array jaisa hai but sirf **unique values** rakhta hai — duplicates automatically ignore ho jaate hain. **`WeakMap`/`WeakSet`** special versions hain jo memory management ko better handle karte hain (garbage collection allow karte hain).

## 4. Technical Explanation

**`Map`:**
- Keys: koi bhi type (objects, functions, primitives).
- Order: insertion order maintained.
- Size: `.size` property (object mein `Object.keys(obj).length` karna padta).

**`Set`:**
- Values: unique only, koi bhi type.
- Order: insertion order maintained.

**`WeakMap`/`WeakSet`:**
- Keys (WeakMap) / Values (WeakSet) **must be objects**.
- Garbage collector unhe collect kar sakta hai agar kahi aur reference na ho — "weak" reference.
- Not iterable, no `.size`, no `for...of` — privacy/memory-focused, general-purpose collection nahi.

## 5. Syntax

```js
const map = new Map();
map.set(key, value);
map.get(key);
map.has(key);
map.delete(key);

const set = new Set([1, 2, 3]);
set.add(value);
set.has(value);
set.delete(value);
```

## 6. Basic Examples

```js
const map = new Map();
map.set("name", "Aryan");
map.set(1, "number key");
map.set(true, "boolean key");

console.log(map.get("name")); // "Aryan"
console.log(map.size);          // 3

const set = new Set([1, 2, 2, 3, 3, 3]);
console.log(set);      // Set(3) {1, 2, 3} — duplicates removed
console.log(set.size); // 3
```

## 7. Detailed Examples

**Why `Map` over plain objects — object keys as keys:**
```js
const objKey1 = { id: 1 };
const objKey2 = { id: 2 };

const map = new Map();
map.set(objKey1, "First object's data");
map.set(objKey2, "Second object's data");

console.log(map.get(objKey1)); // "First object's data"
```
Plain objects sirf string/symbol keys support karte hain — objects ko directly key ki tarah use nahi kar sakte (wo automatically string mein convert ho jaate, `"[object Object]"`, aur sab same key ban jaate). `Map` ye limitation nahi rakhta.

**Removing duplicates from an array using `Set` — a very common real-world pattern:**
```js
const numbers = [1, 2, 2, 3, 4, 4, 5];
const uniqueNumbers = [...new Set(numbers)];
console.log(uniqueNumbers); // [1, 2, 3, 4, 5]
```

**Iterating a `Map`:**
```js
const userRoles = new Map([
  ["admin", "Full access"],
  ["editor", "Can edit content"],
  ["viewer", "Read-only access"]
]);

for (const [role, description] of userRoles) {
  console.log(`${role}: ${description}`);
}
```

**`WeakMap` — private data storage pattern (an alternative to closures for classes):**
```js
const privateData = new WeakMap();

class User {
  constructor(name, ssn) {
    this.name = name; // Public
    privateData.set(this, { ssn }); // "Private" — stored outside the object itself
  }
  getSSN() {
    return privateData.get(this).ssn;
  }
}

const user = new User("Naina", "123-45-6789");
console.log(user.name);       // "Naina"
console.log(user.getSSN());    // "123-45-6789"
console.log(user.ssn);          // undefined — not directly on the object!
```
`WeakMap` ka key yaha `this` (user instance) hai. Jab `user` object garbage-collected hota hai (no more references), `WeakMap` ki entry bhi automatically clean ho jaati hai — memory leak nahi hota, jo ek regular `Map` ke saath ho sakta tha.

## 8. Mental Model

`Map` ko socho ek **advanced dictionary** — object jaisa but koi bhi type ki "word" (key) allowed hai. `Set` ko socho ek **guest list jaha duplicate naam automatically reject ho jaate hain**. `WeakMap` ko socho ek **sticky note jo automatically gir jaata hai jab uska attached object (page) fenk diya jaata hai** — cleanup automatic hai.

## 9. What Happens Internally?

`Map`/`Set` internally hash-table jaisi structure use karte hain jo efficient key-based lookup (`O(1)` roughly) allow karti hai, plain objects ke comparison mein extra type-flexibility ke saath. `WeakMap`/`WeakSet` engine ko batate hain ki ye references "weak" hain — garbage collector unhe roadblock nahi maanta memory-freeing decisions mein.

## 10. Common Mistakes

- `Map`/`Set` ko `for...in` se iterate karne ki koshish karna — `for...of` use karo (ye iterable hain, `for...in` object-property-style iteration ke liye hai).
- `WeakMap`/`WeakSet` mein primitive values (string, number) ko key/value banane ki koshish karna — sirf objects allowed hain.
- Regular object ko duplicates-removal ke liye use karna jab `Set` zyada semantically correct aur concise hota.

## 11. Edge Cases

```js
const set = new Set();
set.add(NaN);
set.add(NaN);
console.log(set.size); // 1 — Set treats NaN as equal to itself (unlike ===)!
```
`Set` "SameValueZero" algorithm use karta hai equality check ke liye, jo `NaN` ko special-case handle karta hai (unlike `===` jaha `NaN === NaN` false hai).

## 12. Real-World Usage

`Set` array deduplication, unique-tag-tracking (jaise blog post tags) mein common hai. `Map` complex caching systems, ya jab object keys chahiye hon. `WeakMap` DOM-element-associated-data (jaise React internals), private class data mein use hota hai.

## 13. Comparison With Related Concepts

| Structure | Similar To | Key Difference |
|-----------|--------------|--------------------|
| `Map` | Object | Any key type, iteration order guaranteed, `.size` |
| `Set` | Array | Only unique values |
| `WeakMap` | Map | Object-only keys, garbage-collectible |
| `WeakSet` | Set | Object-only values, garbage-collectible |

## 14. Practice Questions

1. `Map` object se kaise different hai (kam se kam 2 points)?
2. `Set` se array duplicates kaise remove kiye ja sakte hain?
3. `WeakMap` ka memory-related advantage kya hai regular `Map` ke comparison mein?

## 15. Challenge

Ek function `countWordFrequency(text)` likho jo `Map` use karke ek sentence mein har word ki frequency count kare aur Map return kare.

## 16. Interview Questions

**Q1: `Map` aur plain Object mein kya difference hai, aur kab `Map` use karna better hai?**
A: `Map` **kisi bhi type** ki key support karta hai (objects, functions bhi), jabki plain objects sirf strings/symbols. `Map` insertion order guaranteed maintain karta hai aur `.size` property directly deta hai (object mein `Object.keys().length` karna padta). `Map` bhi prototype pollution issues se safe hai (koi inherited properties nahi jo accidentally key names se clash karein). Jab keys non-string types honi ho, ya frequent add/remove ke saath size tracking chahiye ho, `Map` better choice hai.

**Q2: `WeakMap` regular `Map` se kaise different hai aur iska use-case kya hai?**
A: `WeakMap` ki keys **hamesha objects** honi chahiye (primitives allowed nahi), aur ye keys ko "weakly" hold karta hai — matlab agar us object ka koi aur reference nahi bacha (sirf `WeakMap` mein hai), garbage collector usse memory se remove kar sakta hai, aur `WeakMap` ki wo entry automatically clean ho jaati hai. Ye "private data" associate karne ke liye useful hai objects ke saath (jaise class instances) bina memory leak ka risk liye — regular `Map` ye automatic cleanup nahi karta, kyunki wo apni keys ko "strongly" hold karta hai.

## 17. Chapter Summary

`Map` flexible key-value storage deta hai (any key type). `Set` unique-values collection ke liye hai. `WeakMap`/`WeakSet` object-only keys/values ke saath aate hain aur automatic garbage collection allow karte hain — private data aur memory-sensitive associations ke liye useful.

## 18. Revision Checklist

- [ ] `Map` vs Object ka use-case difference clear hai.
- [ ] `Set` se duplicates remove karna (array deduplication pattern) yaad hai.
- [ ] `WeakMap`/`WeakSet` ka garbage-collection-friendly behavior samajh gaya.

---

**Next:** [05-Iterators-and-Generators.md](./05-Iterators-and-Generators.md)
