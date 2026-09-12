# Symbols

**Module:** 10-Modern-JavaScript
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [05-Iterators-and-Generators.md](./05-Iterators-and-Generators.md)
**Next:** [07-Private-Fields-and-Other-Features.md](./07-Private-Fields-and-Other-Features.md)

---

## 1. Learning Objectives

- Symbol primitive type ka purpose samajhna.
- Symbols se unique, collision-free object keys banana.
- Well-known symbols (jaise `Symbol.iterator`) ka concept revise karna.

## 2. Prerequisites

[02-Variables-and-Data-Types/04-Primitive-Types.md](../02-Variables-and-Data-Types/04-Primitive-Types.md), Module 10 chapter 05.

## 3. Concept in Simple Hinglish

**Symbol** ek primitive type hai jo **hamesha unique** value banata hai — chahe tum same description ke saath 100 symbols banao, sab alag-alag "identities" honge. Ye object properties ke liye ek aisi key banane mein help karta hai jo kabhi bhi accidentally kisi dusri key se collide (clash) nahi karegi.

## 4. Technical Explanation

`Symbol()` function har call pe ek **naya, completely unique** value return karta hai. Symbols object property keys ki tarah use ho sakte hain — ye normal `Object.keys()`, `for...in`, `JSON.stringify()` mein **nahi dikhte**, isliye "hidden"/"non-colliding" metadata ke liye perfect hain.

## 5. Syntax

```js
const sym1 = Symbol("description");
const sym2 = Symbol("description"); // Different from sym1, even with same description!

const obj = {
  [sym1]: "value"
};
```

## 6. Basic Examples

```js
const id1 = Symbol("id");
const id2 = Symbol("id");

console.log(id1 === id2); // false — always unique, even with same description!
console.log(typeof id1);  // "symbol"
```

## 7. Detailed Examples

**Why symbols solve the "key collision" problem:**
```js
const nameKey = Symbol("name");

const user = {
  [nameKey]: "Confidential Name",
  name: "Public Name" // A REGULAR string key — completely separate from the symbol key!
};

console.log(user.name);      // "Public Name"
console.log(user[nameKey]);   // "Confidential Name"
console.log(Object.keys(user)); // ["name"] — symbol key doesn't show up!
```
Do libraries agar independently ek object mein `"name"` key use karna chahti hain bina ek dusre ko affect kiye, symbols ye guarantee dete hain ki unki keys kabhi collide nahi karengi — har `Symbol()` call unique hai.

**Symbols are hidden from normal enumeration:**
```js
const metaKey = Symbol("metadata");
const config = {
  theme: "dark",
  [metaKey]: { version: "1.0", internal: true }
};

console.log(Object.keys(config));                 // ["theme"] — symbol hidden
console.log(JSON.stringify(config));               // '{"theme":"dark"}' — symbol excluded!
console.log(Object.getOwnPropertySymbols(config));  // [Symbol(metadata)] — special method needed to see it
```

**Well-known symbols — customizing built-in behavior (preview, connects to Module 10 chapter 05):**
```js
const customIterable = {
  data: [10, 20, 30],
  [Symbol.iterator]() {
    let index = 0;
    const data = this.data;
    return {
      next: () => index < data.length
        ? { value: data[index++], done: false }
        : { value: undefined, done: true }
    };
  }
};

console.log([...customIterable]); // [10, 20, 30] — using the well-known Symbol.iterator
```
`Symbol.iterator` ek "well-known symbol" hai — JavaScript engine khud isse specific purposes (jaise `for...of` support) ke liye use karta hai. Dusre well-known symbols hain `Symbol.toPrimitive` (Module 09, chapter 04 se yaad karo), `Symbol.hasInstance`, `Symbol.asyncIterator`.

**`Symbol.for()` — globally shared symbols:**
```js
const globalSym1 = Symbol.for("shared");
const globalSym2 = Symbol.for("shared");
console.log(globalSym1 === globalSym2); // true — Symbol.for() reuses symbols from a global registry!
```
`Symbol()` hamesha naya banata hai, lekin `Symbol.for()` ek global registry se same-key symbol ko **reuse** karta hai — ye rare use-case hai jab cross-module symbol sharing chahiye.

## 8. Mental Model

Symbol ko socho ek **unique fingerprint** — chahe do logon ka naam same ho ("John"), unka fingerprint hamesha alag hoga. Object property key ki tarah symbol use karna, "naam clash" ki tension khatam kar deta hai — har symbol apna unique fingerprint carry karta hai.

## 9. What Happens Internally?

Engine har `Symbol()` call pe internally ek unique, unforgeable identity token generate karta hai — ye string se completely different hai (`Symbol("x") !== "x"`), aur koi bhi 2 symbols equal nahi ho sakte unless wo literally same reference hon (ya `Symbol.for()` se same registry entry).

## 10. Common Mistakes

- Symbols ko strings jaisa treat karne ki koshish karna (`Symbol("x") + ""` — `TypeError` deta hai, explicit `.toString()` chahiye).
- Symbols ko "encryption"/"security" ke liye samajhna — ye sirf **naming collision avoidance** ke liye hain, security feature nahi (`Object.getOwnPropertySymbols()` se abhi bhi access ho sakte hain).
- `Symbol()` aur `Symbol.for()` ko confuse karna — pehla hamesha unique, dusra global-registry-shared.

## 11. Edge Cases

```js
console.log(Symbol("x") === Symbol("x")); // false
console.log(Symbol.for("x") === Symbol.for("x")); // true
// console.log(`${Symbol("x")}`); // ❌ TypeError — symbols can't be implicitly converted to string
console.log(Symbol("x").toString()); // "Symbol(x)" — explicit conversion works
```

## 12. Real-World Usage

Symbols library-internal "private-ish" metadata ke liye use hote hain (jaise React ke internal `$$typeof` fields, though React specifically uses actual symbols for this). Well-known symbols (`Symbol.iterator`, `Symbol.toPrimitive`) custom objects ka behavior built-in JavaScript features ke saath integrate karne ke liye use hote hain.

## 13. Comparison With Related Concepts

| Approach | Collision-Safe? | Hidden from enumeration? |
|----------|-------------------|--------------------------------|
| String keys | ❌ No | ❌ No |
| Symbol keys | ✅ Yes | ✅ Yes |
| Private fields (`#field`, next chapter) | ✅ Yes (compile-time enforced) | ✅ Yes |

## 14. Practice Questions

1. `Symbol("id") === Symbol("id")` — true ya false, aur kyun?
2. Symbols `Object.keys()` mein kyun nahi dikhte?
3. `Symbol.for()` `Symbol()` se kaise different hai?

## 15. Challenge

Ek object banao jisme ek regular string key `type` ho aur ek symbol key (`Symbol("type")`) bhi ho — dikhao ki dono independently exist karte hain bina clash ke, aur `Object.keys()` sirf string key dikhata hai.

## 16. Interview Questions

**Q1: JavaScript mein Symbols ka main purpose kya hai?**
A: Symbols ek unique, immutable primitive value banate hain jo primarily **object property keys mein naming collisions avoid karne** ke liye use hoti hai. Har `Symbol()` call — chahe same description ke saath ho — ek completely unique value return karta hai. Ye libraries/frameworks ko apni "private-ish" metadata ek object mein add karne deta hai bina kisi existing ya future string-key se accidentally clash kiye.

**Q2: `Symbol.iterator` kya hai?**
A: `Symbol.iterator` ek "well-known symbol" hai jo JavaScript engine internally use karta hai decide karne ke liye ki koi object `for...of` loop, spread operator, ya destructuring ke saath "iterable" hai ya nahi. Agar koi object apne andar `[Symbol.iterator]` method define karta hai (jo ek valid iterator return kare), wo object built-in iteration syntax ke saath seamlessly kaam karega — ye custom data structures ko native JavaScript features ke saath integrate karne ka standard tareeka hai.

## 17. Chapter Summary

Symbols unique primitive values hain, mainly object keys mein naming-collision avoid karne ke liye use hote hain. Ye normal enumeration (`Object.keys`, `for...in`, `JSON.stringify`) se hidden rehte hain. Well-known symbols (`Symbol.iterator`, `Symbol.toPrimitive`) custom objects ko built-in JavaScript behaviors ke saath integrate karne dete hain.

## 18. Revision Checklist

- [ ] Symbols ki "always unique" nature clear hai.
- [ ] Symbols normal enumeration se hidden hote hain, ye yaad hai.
- [ ] `Symbol.iterator` ka role samajh gaya.

---

**Next:** [07-Private-Fields-and-Other-Features.md](./07-Private-Fields-and-Other-Features.md)
