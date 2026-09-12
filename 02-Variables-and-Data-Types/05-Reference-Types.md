# Reference Types

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [04-Primitive-Types.md](./04-Primitive-Types.md)
**Next:** [06-Type-Checking.md](./06-Type-Checking.md)

---

## 1. Learning Objectives

- Reference types (objects, arrays, functions) ka "copy by reference" behavior samajhna.
- Deep copy vs shallow copy ka difference jaanna.
- Reference equality vs value equality samajhna.

## 2. Prerequisites

[04-Primitive-Types.md](./04-Primitive-Types.md)

## 3. Concept in Simple Hinglish

Reference types (objects, arrays, functions) primitives se alag tarike se kaam karte hain — jab tum inhe kisi variable mein copy karte ho, actual data copy nahi hota, balki us data ka **address (reference)** copy hota hai. Isi liye do variables **same object ko point** kar sakte hain — ek mein change dusre mein bhi dikhega.

## 4. Technical Explanation

JavaScript mein sabhi non-primitive values (`object`, `array`, `function`, `Map`, `Set`, `Date`, etc.) **reference types** hain. Ye memory mein "heap" pe store hote hain, aur variable sirf us memory location ka reference (pointer) hold karta hai.

Jab tum `let b = a;` karte ho aur `a` ek object hai, `b` ko object ka **wahi reference** milta hai — koi naya object nahi banta.

## 5. Syntax

```js
let obj1 = { value: 10 };
let obj2 = obj1;   // reference copy, not value copy

let arr1 = [1, 2, 3];
let arr2 = arr1;   // reference copy
```

## 6. Basic Examples

```js
let original = { name: "Aisha" };
let copy = original;

copy.name = "Zara";
console.log(original.name); // "Zara" — original bhi change ho gaya!
```

## 7. Detailed Examples

**Reference equality:**
```js
let objA = { value: 1 };
let objB = { value: 1 };
let objC = objA;

console.log(objA === objB); // false — different objects, even though content same
console.log(objA === objC); // true — same reference
```

`===` objects ke liye **reference** compare karta hai, content nahi. Isi liye `objA` aur `objB` ka data same hone par bhi `false` aata hai — wo alag-alag memory locations pe hain.

**Shallow copy — object spread se:**
```js
let user = { name: "Kunal", address: { city: "Delhi" } };

let userCopy = { ...user }; // Shallow copy
userCopy.name = "Kunal 2";
userCopy.address.city = "Mumbai"; // ⚠️ nested object still shared!

console.log(user.name);         // "Kunal" — top-level property safe
console.log(user.address.city); // "Mumbai" — nested object still reference-shared!
```

Spread operator (`...`) sirf **top-level** properties ka naya copy banata hai — andar ke nested objects/arrays abhi bhi original ke saath reference-shared hote hain. Isi ko "shallow copy" kehte hain.

**Deep copy — poori tarah independent:**
```js
let user = { name: "Kunal", address: { city: "Delhi" } };

let deepCopy = JSON.parse(JSON.stringify(user)); // Simple deep copy trick
deepCopy.address.city = "Mumbai";

console.log(user.address.city); // "Delhi" — unaffected!
```

`JSON.parse(JSON.stringify())` ek quick deep-copy trick hai, lekin functions, `undefined`, `Symbol`, `Date` jaisi values ko sahi se handle nahi karta. Modern JavaScript mein `structuredClone(obj)` (browser + Node.js 17+) ek better native deep-copy method hai.

## 8. Mental Model

Object ko socho ek **ghar ka address** ki tarah — variable us address ki chitthi (reference) rakhta hai, ghar khud nahi. Agar do log same address ki chitthi rakhte hain aur ek ghar mein furniture rearrange karta hai, dusra bhi wahi changed ghar dekhega — kyunki dono same actual ghar ka address rakhte hain.

## 9. What Happens Internally?

Engine memory ko do parts mein manage karta hai — **stack** (primitives aur references store hote hain) aur **heap** (actual object data store hota hai). Jab `let obj = {}` likhte ho, heap mein object banta hai, aur stack pe `obj` variable ek pointer/reference rakhta hai us heap location ka.

## 10. Common Mistakes

- Object/array ko "copy" karne ke liye seedha assignment (`let copy = original`) use karna — ye reference copy karta hai, naya object nahi banata.
- Shallow copy ko deep copy samajhna — nested objects still shared rehte hain.
- `===` se objects compare karke content-equality expect karna — actually reference-equality check hoti hai.

## 11. Edge Cases

- Functions bhi reference types hain — function ko variable mein assign karna reference copy karta hai.
- Arrays object hi hote hain internally (`typeof [] === "object"`) — isliye arrays bhi reference-type behavior follow karte hain.
- `Object.freeze()` object ko shallow-immutable banata hai — nested objects abhi bhi mutate ho sakte hain.

## 12. Real-World Usage

React jaisi libraries mein "state ko directly mutate mat karo" rule isi reference-type behavior ki wajah se hai — agar tum object ko directly mutate karo, React ko change detect karna difficult ho jaata hai (reference same rehta hai). Isi liye naya object/array banaya jaata hai (spread operator ya `map`/`filter` se) state updates ke liye.

## 13. Comparison With Related Concepts

| Aspect | Primitive | Reference |
|--------|-----------|-----------|
| Copy behavior | Independent value | Shared reference |
| Equality check (`===`) | Value comparison | Reference comparison |
| Mutation | Not possible | Possible (unless frozen) |
| Storage | Stack (conceptually) | Heap |

## 14. Practice Questions

1. Shallow copy aur deep copy mein kya difference hai?
2. `{a: 1} === {a: 1}` — result kya hoga aur kyun?
3. `Object.freeze()` deep immutability deta hai ya shallow?

## 15. Challenge

Ek nested object banao (`{ name: "X", settings: { theme: "dark" } }`), ek shallow copy banao spread operator se, phir dono ke `settings.theme` change karke observe karo kya hota hai. Phir `structuredClone()` (ya `JSON.parse/stringify`) use karke same experiment repeat karo.

## 16. Interview Questions

**Q1: JavaScript mein "pass by value" aur "pass by reference" kaise kaam karte hain?**
A: JavaScript technically dono ko "pass by value" hi treat karta hai, lekin reference types ke case mein us "value" khud ek reference (memory address) hoti hai. Isliye primitives assign/pass karne pe independent copy milta hai, jabki objects/arrays assign/pass karne pe reference ki copy milti hai — jo same underlying data ko point karti hai. Isi wajah se object mutate karne pe original bhi affect hota hai.

**Q2: Shallow copy aur deep copy mein difference batao example ke saath.**
A: Shallow copy sirf top-level properties ka naya copy banata hai (jaise spread operator `{...obj}`), lekin nested objects/arrays abhi bhi original ke saath reference-share hote hain. Deep copy poori structure ko recursively naya banata hai — nested objects bhi independent hote hain. Deep copy ke liye `structuredClone()`, lodash ka `cloneDeep`, ya `JSON.parse(JSON.stringify())` (limitations ke saath) use hota hai.

## 17. Chapter Summary

Objects, arrays, aur functions reference types hain — variable mein unka reference (pointer) store hota hai, actual value nahi. Isse "copy by reference" behavior aata hai, jaha shared object mutate karne se sab references affected hote hain. Shallow copy sirf top-level, deep copy poori structure ko independent banata hai.

## 18. Revision Checklist

- [ ] Reference vs primitive copy behavior clear hai.
- [ ] Shallow copy vs deep copy ka difference example se samajh gaya.
- [ ] `===` object comparison mein reference check karta hai, ye pata hai.

---

**Next:** [06-Type-Checking.md](./06-Type-Checking.md)
