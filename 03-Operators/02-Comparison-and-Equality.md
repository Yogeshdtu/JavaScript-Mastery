# Comparison and Equality

**Module:** 03-Operators
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [01-Arithmetic-and-Assignment-Operators.md](./01-Arithmetic-and-Assignment-Operators.md)
**Next:** [03-Logical-Nullish-Optional-Chaining.md](./03-Logical-Nullish-Optional-Chaining.md)

---

## 1. Learning Objectives

- `==` (loose equality) aur `===` (strict equality) ka exact difference samajhna.
- Comparison operators (`<`, `>`, `<=`, `>=`) ka string/number behavior jaanna.
- `Object.is()` aur special comparisons (`NaN`, `-0`) samajhna.

## 2. Prerequisites

Module 03, chapter 01.

## 3. Concept in Simple Hinglish

`==` do values ko compare karte time unka **type ignore** karke, coerce (convert) karke compare karta hai. `===` **type bhi match** karta hai — koi conversion nahi hoti. Yahi wajah hai `===` ko "strict equality" kehte hain, aur professional code mein hamesha ise use karna recommend kiya jaata hai.

## 4. Technical Explanation

**`==` (Loose/Abstract Equality):** Comparison se pehle type coercion perform karta hai agar types different hain.

**`===` (Strict Equality):** Sirf tab `true` deta hai jab **type aur value dono** match karein — koi coercion nahi.

Poori type coercion detail [09-Type-Coercion](../09-Type-Coercion/) module mein hai — abhi comparison operators ke context mein basic samajhte hain.

## 5. Syntax

```js
a == b     // loose equality
a === b    // strict equality
a != b     // loose inequality
a !== b    // strict inequality
a < b, a > b, a <= b, a >= b
```

## 6. Basic Examples

```js
console.log(5 == "5");   // true  (string coerced to number)
console.log(5 === "5");  // false (different types)
console.log(5 === 5);    // true
```

## 7. Detailed Examples

```js
console.log(0 == false);        // true  (false → 0)
console.log(0 === false);       // false (different types)

console.log(null == undefined); // true  (special case rule)
console.log(null === undefined);// false

console.log("" == 0);           // true  ("" → 0)
console.log(NaN == NaN);        // false ⚠️ NaN is never equal to anything, even itself!
```

**Reliable NaN check:**
```js
console.log(Number.isNaN(NaN));       // true
console.log(Object.is(NaN, NaN));     // true — Object.is handles this edge case correctly
```

**String comparison — lexicographic (dictionary) order:**
```js
console.log("apple" < "banana"); // true  (a comes before b)
console.log("Zebra" < "apple");  // true  (uppercase letters have lower char codes!)
```

Yahan `"Zebra" < "apple"` true hai kyunki string comparison character codes pe based hai — uppercase letters (`A`-`Z` = 65-90) ka code lowercase (`a`-`z` = 97-122) se chhota hota hai. Isi liye case-sensitive comparisons mein unexpected results aa sakte hain — case-insensitive comparison ke liye `.toLowerCase()` use karo.

## 8. Mental Model

`==` ko socho ek **flexible security guard** jo different "uniforms" (types) ignore karke sirf pehchaan (value) check karta hai. `===` ek **strict security guard** hai jo uniform (type) aur pehchaan (value) dono match hona chahiye.

## 9. What Happens Internally?

`==` operator internally "Abstract Equality Comparison Algorithm" follow karta hai (ECMAScript spec mein defined) — ye specific rules ke through decide karta hai kaunsi value ko kis type mein convert karna hai comparison se pehle. `===` ye entire process skip karta hai — directly type aur value compare karta hai, isliye ye **faster aur predictable** bhi hota hai.

## 10. Common Mistakes

- `==` ko default choice banana — hamesha `===` use karo jab tak coercion intentionally na chahiye ho.
- `NaN` ko `==`/`===` se check karna — `NaN` kabhi kisi se equal nahi hota, `Number.isNaN()` use karo.
- Object comparison mein confusion — `{} == {}` bhi `false` hai (reference comparison, coercion applicable nahi hoti objects ke beech).

## 11. Edge Cases

```js
console.log([] == false);  // true  ([] → "" → 0, false → 0)
console.log([] == ![]);    // true  (! operator [] ko false bana deta hai, phir coercion chain)
```
Ye examples dikhate hain `==` ki coercion chain kitni unpredictable ho sakti hai — is liye production code `===` prefer karta hai.

## 12. Real-World Usage

**✅ Modern / Recommended:** Har jagah `===` aur `!==` use karo, sirf `== null` ek accepted exception hai jo `null` aur `undefined` dono ko ek saath check karta hai (`value == null` matches both `null` and `undefined`).

**⚠️ Legacy:** Purane codebases mein `==` common milega — inhe padhte waqt dhyaan rakhna coercion rules kya apply ho rahi hain.

## 13. Comparison With Related Concepts

| Operator | Type Coercion? | Recommended? |
|----------|------------------|----------------|
| `==` | Yes | ⚠️ Avoid (except `== null`) |
| `===` | No | ✅ Always prefer |
| `Object.is()` | No (handles NaN, -0 correctly) | ✅ Special edge cases ke liye |

## 14. Practice Questions

1. `"5" == 5` aur `"5" === 5` ke results kya honge?
2. `null == undefined` true hai ya false?
3. `NaN` ko reliably check karne ka sahi tareeka kya hai?

## 15. Challenge

10 comparison expressions likho jinme se 5 `true` return karein aur 5 `false` — each ko bina run kiye predict karo, phir verify karo.

## 16. Interview Questions

**Q1: `==` aur `===` mein difference batao, examples ke saath.**
A: `==` (loose equality) comparison se pehle operands ko same type mein coerce karta hai — jaise `5 == "5"` true hai kyunki string "5" number 5 mein convert ho jaati hai. `===` (strict equality) koi coercion nahi karta — type aur value dono match hone chahiye, isliye `5 === "5"` false hai. Best practice hai `===` use karna taaki unpredictable coercion bugs na aaye.

**Q2: `NaN == NaN` false kyun hai?**
A: Ye IEEE 754 floating-point specification ka rule hai — `NaN` (Not-a-Number) ko definition ke hisaab se kisi bhi value ke, khud ke saath bhi, equal nahi mana jaata, kyunki `NaN` ek "undefined/unrepresentable numeric result" ko represent karta hai, ek specific value nahi. Isi liye `NaN` check karne ke liye `Number.isNaN(value)` ya `Object.is(value, NaN)` use kiya jaata hai, `==`/`===` nahi.

## 17. Chapter Summary

`==` type coercion ke saath compare karta hai, jabki `===` strict type+value match check karta hai — professional code `===` prefer karta hai. `NaN` special hai — kisi se bhi equal nahi hota. String comparisons character codes pe based hote hain, jisse case-sensitivity matter karti hai.

## 18. Revision Checklist

- [ ] `==` vs `===` ka coercion behavior clear hai.
- [ ] `NaN` comparison ka quirk yaad hai.
- [ ] String lexicographic comparison samajh gaya.

---

**Next:** [03-Logical-Nullish-Optional-Chaining.md](./03-Logical-Nullish-Optional-Chaining.md)
