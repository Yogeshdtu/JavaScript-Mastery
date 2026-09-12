# Object Methods (Object.keys, values, entries, assign, freeze, seal)

**Module:** 07-Objects
**Difficulty:** 🟡 Intermediate
**Previous:** [02-Destructuring-and-Spread.md](./02-Destructuring-and-Spread.md)
**Next:** [04-Property-Descriptors.md](./04-Property-Descriptors.md)

---

## 1. Learning Objectives

- `Object.keys()`, `Object.values()`, `Object.entries()` se object data extract karna.
- `Object.assign()` se objects merge karna.
- `Object.freeze()` aur `Object.seal()` se immutability control karna.

## 2. Prerequisites

Module 07, chapters 01-02.

## 3. Concept in Simple Hinglish

JavaScript ka built-in `Object` class kuch **static methods** deti hai objects ko inspect/manipulate karne ke liye — jaise saari keys nikaalna, saari values nikaalna, ya object ko "freeze" karke change hone se rokna.

## 4. Technical Explanation

| Method | Returns |
|--------|---------|
| `Object.keys(obj)` | Array of property names |
| `Object.values(obj)` | Array of property values |
| `Object.entries(obj)` | Array of `[key, value]` pairs |
| `Object.assign(target, ...sources)` | Merges sources into target (mutates target!) |
| `Object.freeze(obj)` | Prevents ANY changes (add/remove/modify) |
| `Object.seal(obj)` | Prevents add/remove, but allows modifying existing values |

## 5. Syntax

```js
Object.keys(obj);
Object.values(obj);
Object.entries(obj);
Object.assign(target, source1, source2);
Object.freeze(obj);
Object.seal(obj);
```

## 6. Basic Examples

```js
const user = { name: "Divya", age: 27 };

console.log(Object.keys(user));   // ["name", "age"]
console.log(Object.values(user)); // ["Divya", 27]
console.log(Object.entries(user)); // [["name", "Divya"], ["age", 27]]
```

## 7. Detailed Examples

**`Object.entries()` + `for...of` — the modern object iteration pattern:**
```js
const scores = { math: 90, science: 85, english: 78 };

for (const [subject, score] of Object.entries(scores)) {
  console.log(`${subject}: ${score}`);
}
```

**`Object.assign()` — merging, and its mutation trap:**
```js
const target = { a: 1 };
const source = { b: 2 };

const result = Object.assign(target, source);
console.log(result); // { a: 1, b: 2 }
console.log(target);  // { a: 1, b: 2 } ⚠️ target itself got mutated!

// ✅ Safer way — use an empty object as target to avoid mutating originals
const safeMerge = Object.assign({}, target, source);
// Or, the modern equivalent using spread:
const safeMerge2 = { ...target, ...source };
```
`Object.assign()` **first argument (target) ko mutate karta hai** — agar tum kisi existing object ko preserve karna chahte ho, empty object `{}` ko target ki tarah use karo, ya modern spread syntax use karo.

**`Object.freeze()` — making an object truly read-only (shallow):**
```js
const config = Object.freeze({ apiUrl: "https://api.example.com", timeout: 5000 });

config.timeout = 10000; // Silently fails (throws in strict mode)
console.log(config.timeout); // 5000 — unchanged

console.log(Object.isFrozen(config)); // true
```

**`Object.freeze()` is shallow — a critical gotcha:**
```js
const settings = Object.freeze({
  theme: "dark",
  nested: { fontSize: 14 }
});

settings.nested.fontSize = 20; // ✅ This WORKS! Nested object is NOT frozen
console.log(settings.nested.fontSize); // 20
```
`Object.freeze()` sirf **top-level** properties ko lock karta hai — nested objects abhi bhi mutable rehte hain. Deep-freeze ke liye recursively freeze karna padta hai (custom function ya library).

**`Object.seal()` — allows modification, prevents structural changes:**
```js
const sealedObj = Object.seal({ x: 1 });

sealedObj.x = 100;   // ✅ Allowed — modifying existing property
sealedObj.y = 200;   // ❌ Silently fails — cannot ADD new property
delete sealedObj.x;  // ❌ Silently fails — cannot DELETE property

console.log(sealedObj); // { x: 100 }
```

## 8. Mental Model

`Object.keys/values/entries` ko socho ek **filing cabinet ke labels, files, ya label-file pairs ki list banana**. `Object.freeze()` ko socho **object ko cement mein set kar dena** — kuch bhi change nahi ho sakta (top-level). `Object.seal()` ko socho **cabinet ko lock kar dena naye drawers add/remove karne se, but existing drawers ke andar ka saman change kar sakte ho**.

## 9. What Happens Internally?

`Object.freeze()`/`Object.seal()` object ke internal **property descriptors** (next chapter) ko modify karte hain — `writable`, `configurable` flags ko `false` set kar dete hain relevant properties ke liye. Ye ek engine-level enforcement hai, not just convention.

## 10. Common Mistakes

- `Object.freeze()` ko deep-immutability samajhna — ye sirf shallow hai.
- `Object.assign()` ko bina empty target object ke use karna aur accidentally original object mutate kar dena.
- Non-strict mode mein frozen object ko modify karne ki koshish karna aur expect karna ki error aayega — ye **silently fail** hota hai (strict mode mein `TypeError` aata hai).

## 11. Edge Cases

```js
console.log(Object.keys([10, 20, 30])); // ["0", "1", "2"] — works on arrays too (as string indices)!
console.log(Object.keys("hi"));          // ["0", "1"] — even works on strings!
```

## 12. Real-World Usage

`Object.freeze()` constants/configuration objects ko immutable banane ke liye use hota hai (jaise app-wide constants). `Object.entries()` object ko table/list mein render karne ke liye (React mein common). `Object.assign()`/spread state-management libraries mein immutable updates ke liye.

## 13. Comparison With Related Concepts

| Method | Prevents Add | Prevents Delete | Prevents Modify |
|--------|----------------|-------------------|---------------------|
| `Object.seal()` | ✅ Yes | ✅ Yes | ❌ No (still allowed) |
| `Object.freeze()` | ✅ Yes | ✅ Yes | ✅ Yes |

## 14. Practice Questions

1. `Object.freeze()` aur `Object.seal()` mein kya difference hai?
2. `Object.assign()` apna first argument mutate karta hai — is problem ko kaise avoid karte hain?
3. `Object.freeze()` deep ya shallow hota hai?

## 15. Challenge

Ek object `appConfig = { apiUrl: "...", limits: { maxUsers: 100 } }` banao. Isse freeze karo, phir dikhao ki top-level property change nahi hoti but `limits.maxUsers` change ho jaata hai — is behavior ko explain karo comments mein.

## 16. Interview Questions

**Q1: `Object.freeze()` object ko poori tarah immutable bana deta hai — kya ye statement sahi hai?**
A: Poori tarah sahi nahi hai. `Object.freeze()` sirf **shallow immutability** deta hai — object ki **top-level** properties add/remove/modify nahi ki ja sakti. Lekin agar koi property khud ek object/array (nested reference) hai, uski andar ki properties **abhi bhi mutable** rehti hain, kyunki `freeze()` sirf outer object pe apply hua tha. Deep immutability ke liye recursively freeze karna padta hai, ya libraries (jaise Immer) use karni padti hain.

**Q2: `Object.assign()` ka use karte time kya dhyaan rakhna chahiye?**
A: `Object.assign(target, ...sources)` **target object ko mutate karta hai** — agar target ek existing, important object hai, wo permanently change ho jaayega. Isse avoid karne ke liye ek naya empty object (`{}`) ko target ki tarah pass karna chahiye: `Object.assign({}, obj1, obj2)`, ya modern spread syntax `{...obj1, ...obj2}` use karna chahiye jo naturally naya object banata hai.

## 17. Chapter Summary

`Object.keys/values/entries` object data ko arrays mein convert karte hain. `Object.assign()` objects merge karta hai but target ko mutate karta hai. `Object.freeze()` shallow immutability deta hai, `Object.seal()` sirf structural changes (add/delete) ko rokta hai, modification allow karta hai.

## 18. Revision Checklist

- [ ] `Object.keys/values/entries` ke outputs yaad hain.
- [ ] `Object.assign()` ka mutation-risk clear hai.
- [ ] `Object.freeze()` ki shallow-ness ka gotcha samajh gaya.

---

**Next:** [04-Property-Descriptors.md](./04-Property-Descriptors.md)
