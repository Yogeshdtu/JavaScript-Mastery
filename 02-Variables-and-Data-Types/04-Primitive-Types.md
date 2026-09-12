# Primitive Types

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟢 Beginner
**Previous:** [03-Hoisting-and-TDZ.md](./03-Hoisting-and-TDZ.md)
**Next:** [05-Reference-Types.md](./05-Reference-Types.md)

---

## 1. Learning Objectives

- JavaScript ke 7 primitive types identify karna.
- `null` vs `undefined` ka exact difference samajhna.
- Primitive values ka "immutable" aur "copied by value" behavior samajhna.

## 2. Prerequisites

Module 02, chapters 01-03.

## 3. Concept in Simple Hinglish

**Primitive types** JavaScript ke sabse basic data types hain — ye directly value store karte hain (memory mein simple, chhoti values). Jab tum ek primitive value ko dusre variable mein copy karte ho, ek **naya independent copy** ban jaata hai — original se koi connection nahi rehta.

## 4. Technical Explanation

JavaScript mein **7 primitive types** hain:

| Type | Example | Description |
|------|---------|-------------|
| `string` | `"hello"` | Text data |
| `number` | `42`, `3.14` | Integers aur decimals |
| `bigint` | `123n` | Bahut bade integers (Number se aage) |
| `boolean` | `true`, `false` | Logical true/false |
| `undefined` | `undefined` | Value assign nahi hui |
| `null` | `null` | Intentionally "no value" |
| `symbol` | `Symbol("id")` | Unique identifier |

Primitives **immutable** hote hain — matlab ek baar bana value change nahi hoti (string ki koi character directly modify nahi kar sakte, naya string banana padta hai). Primitives **"pass by value"** hote hain — copy karne pe independent copy milta hai.

## 5. Syntax

```js
let str = "text";
let num = 42;
let big = 123456789012345678901234567890n;
let bool = true;
let notAssigned;      // undefined
let empty = null;
let uniqueId = Symbol("id");
```

## 6. Basic Examples

```js
console.log(typeof "hello");     // "string"
console.log(typeof 42);          // "number"
console.log(typeof true);        // "boolean"
console.log(typeof undefined);   // "undefined"
console.log(typeof null);        // "object"  ⚠️ famous JS bug/quirk!
console.log(typeof Symbol());    // "symbol"
console.log(typeof 10n);         // "bigint"
```

## 7. Detailed Examples

**Primitives copy by value:**
```js
let a = 10;
let b = a; // b ko a ki value ka COPY milta hai

b = 20;
console.log(a); // 10 — a change nahi hua, kyunki b ek independent copy hai
console.log(b); // 20
```

**null vs undefined:**
```js
let user;             // Declared but not assigned
console.log(user);    // undefined — JS ne khud assign kiya "no value yet"

let selectedUser = null; // Developer ne EXPLICITLY bataya "no value"
console.log(selectedUser); // null

console.log(user == selectedUser);  // true (loose equality — coercion)
console.log(user === selectedUser); // false (different types)
```

`undefined` ka matlab hai "value abhi tak set nahi hui" (JavaScript ka default). `null` ka matlab hai "yahan intentionally koi value nahi hai" (developer ne explicitly set kiya). Ye distinction real code mein important hai — jaise API response mein `null` ka matlab "user ne field khali chhodi" ho sakta hai, jabki `undefined` ka matlab "field hi exist nahi karti".

**String immutability:**
```js
let name = "Rahul";
name[0] = "M"; // Silently fails (no error in non-strict, TypeError in strict)
console.log(name); // "Rahul" — koi change nahi hua

name = "M" + name.slice(1); // Naya string banana hi tareeka hai
console.log(name); // "Mahul"
```

## 8. Mental Model

Primitive value ko socho ek **coin** ki tarah — jab tum coin kisi ko dete ho, wo tumhare coin ki **exact duplicate copy** leta hai, tumhara coin unaffected rehta hai. Reference types (next chapter) ek "shared bank account" ki tarah hote hain — agar do log same account access karte hain, ek ka change dusre ko bhi dikhta hai.

## 9. What Happens Internally?

Primitives JavaScript engine mein "stack" jaisi simple memory structure mein directly store hote hain (conceptually — actual V8 implementation isse zyada complex hai). Jab assignment hoti hai (`let b = a`), engine value ko naye memory location mein copy kar deta hai — dono variables independent ho jaate hain.

## 10. Common Mistakes

- `typeof null === "object"` ko forget karna — ye JavaScript ka historic bug hai jo backward-compatibility ke liye fix nahi kiya gaya.
- `null` aur `undefined` ko interchangeably use karna without thought — professional code mein consistent convention rakhna chahiye (e.g., API missing values ke liye `null` use karo).
- String ko mutate karne ki koshish karna (`str[0] = "x"`) aur wonder karna ki change kyun nahi hua.

## 11. Edge Cases

- `NaN` (Not-a-Number) technically `number` type ka hai: `typeof NaN === "number"`. Aur `NaN === NaN` **false** hai — ye JavaScript ka sabse famous quirk hai (isko check karne ke liye `Number.isNaN()` use karo).
- `BigInt` aur regular `Number` ko directly operators se mix nahi kar sakte (`10n + 5` TypeError dega) — explicit conversion chahiye.

## 12. Real-World Usage

`null` commonly APIs mein "value intentionally empty" ke liye use hota hai (jaise database ka empty field). `BigInt` cryptography, large ID systems (jaise 64-bit database IDs) mein use hota hai jaha regular `Number` precision kho deta hai.

## 13. Comparison With Related Concepts

| Aspect | Primitive Types | Reference Types (next chapter) |
|--------|-------------------|-----------------------------------|
| Storage | Value directly | Reference/pointer to memory |
| Copy behavior | Independent copy | Shared reference |
| Mutability | Immutable | Mutable |
| Examples | string, number, boolean | object, array, function |

## 14. Practice Questions

1. `typeof null` ka result kya hai, aur ye kyun "bug" mana jaata hai?
2. `null` aur `undefined` mein practical difference ka ek real example do.
3. `NaN === NaN` ka result kya hai?

## 15. Challenge

Predict output aur explain:
```js
let x = 5;
let y = x;
x = x + 1;
console.log(x, y);
```

## 16. Interview Questions

**Q1: `null` aur `undefined` mein kya difference hai?**
A: `undefined` ka matlab hai variable declared hai lekin usme koi value assign nahi hui — JavaScript engine automatically ye assign karta hai. `null` ek explicit value hai jo developer khud assign karta hai "intentionally no value" batane ke liye. `typeof undefined` "undefined" hai, jabki `typeof null` "object" hai (historic quirk). Loose equality (`==`) mein dono equal hote hain, but strict equality (`===`) mein nahi.

**Q2: Primitive values "immutable" kyun hoti hain, example ke saath?**
A: Immutable matlab ek baar value ban gayi to wo change nahi hoti — naya value banani padti hai. Jaise string `"hello"` ka koi character directly modify nahi kar sakte (`str[0] = "H"` kaam nahi karega). Concat/replace jaise operations naya string return karte hain, original ko modify nahi karte. Yeh JavaScript engine ko values ko safely share karne deta hai without unexpected side-effects.

## 17. Chapter Summary

JavaScript ke 7 primitive types hain: string, number, bigint, boolean, undefined, null, symbol. Ye immutable hote hain aur "copy by value" behavior follow karte hain. `null` aur `undefined` dono "no value" represent karte hain lekin different intent ke saath.

## 18. Revision Checklist

- [ ] 7 primitive types naam se yaad hain.
- [ ] `null` vs `undefined` ka use-case difference clear hai.
- [ ] "Copy by value" behavior example se samajh gaya.

---

**Next:** [05-Reference-Types.md](./05-Reference-Types.md)
