# Type Checking

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟢 Beginner
**Previous:** [05-Reference-Types.md](./05-Reference-Types.md)
**Next:** [07-Regular-Expressions.md](./07-Regular-Expressions.md)

---

## 1. Learning Objectives

- `typeof` aur `instanceof` operators correctly use karna.
- Types check karne ke reliable tareeke seekhna, especially arrays aur `null` ke liye.

## 2. Prerequisites

Module 02, chapters 01-05.

## 3. Concept in Simple Hinglish

Kabhi-kabhi tumhe pata karna hota hai ki ek variable mein **kaunsa type ki value** hai — number hai, string hai, ya object hai. JavaScript isके liye `typeof` aur `instanceof` operators deta hai — lekin dono ke apne limitations hain jo samajhna zaroori hai.

## 4. Technical Explanation

**`typeof`** operator ek string return karta hai jo value ka primitive type batata hai:
```js
typeof "text"      // "string"
typeof 42           // "number"
typeof true          // "boolean"
typeof undefined     // "undefined"
typeof {}            // "object"
typeof []            // "object" ⚠️ (arrays bhi "object" hain typeof ke liye)
typeof function(){}  // "function"
typeof null          // "object" ⚠️ (historic quirk)
typeof Symbol()      // "symbol"
typeof 10n           // "bigint"
```

**`instanceof`** operator check karta hai ki object kisi specific constructor/class ka instance hai ya nahi:
```js
[] instanceof Array    // true
{} instanceof Object    // true
[] instanceof Object    // true (array bhi object hai)
```

## 5. Syntax

```js
typeof value;
value instanceof ConstructorName;
Array.isArray(value);
```

## 6. Basic Examples

```js
console.log(typeof 100);            // "number"
console.log([1,2,3] instanceof Array); // true
console.log(Array.isArray([1,2,3]));  // true
```

## 7. Detailed Examples

**`typeof` array ke liye reliable nahi hai:**
```js
function isArray(value) {
  return typeof value === "array"; // ❌ Ye kaam nahi karega!
}
console.log(isArray([1,2,3])); // false — WRONG result
```

`typeof []` actually `"object"` return karta hai, `"array"` nahi. Isliye array check karne ka **sahi tareeka**:
```js
function isArrayCorrect(value) {
  return Array.isArray(value); // ✅ Purpose-built method
}
console.log(isArrayCorrect([1,2,3])); // true
```

**`null` check karne ka safe tareeka:**
```js
function isNull(value) {
  return value === null; // ✅ Directly compare karo, typeof use mat karo
}
console.log(isNull(null)); // true
console.log(typeof null);  // "object" — isliye typeof se null detect karna galat approach hai
```

**Complete type-check helper function:**
```js
function getType(value) {
  if (value === null) return "null";
  if (Array.isArray(value)) return "array";
  return typeof value;
}

console.log(getType(null));      // "null"
console.log(getType([1,2,3]));   // "array"
console.log(getType({}));        // "object"
console.log(getType("hi"));      // "string"
```

## 8. Mental Model

`typeof` ko socho ek **basic ID card checker** ki tarah — general category batata hai (string, number, object) lekin fine details (array vs plain object) nahi batata. `instanceof` ek **specific membership checker** hai — batata hai "kya ye specific club (class) ka member hai".

## 9. What Happens Internally?

`typeof` engine ke internal type tag ko check karta hai — ye ek low-level, bahut fast operation hai. `instanceof` prototype chain ko traverse karta hai (Module 10 mein detail) — check karta hai ki constructor ka `prototype` object dusre object ki prototype chain mein kahi milta hai ya nahi.

## 10. Common Mistakes

- Array check karne ke liye `typeof` use karna — always `Array.isArray()` use karo.
- `null` check karne ke liye `typeof value === "object"` use karna — ye objects se bhi match ho jaayega, `value === null` use karo.
- `instanceof` ko primitive values pe use karna aur unexpected results milna (`"text" instanceof String` → `false`, kyunki primitive string ek String object instance nahi hai).

## 11. Edge Cases

- Different JavaScript execution contexts (jaise iframes) mein banaye gaye arrays `instanceof Array` check fail kar sakte hain (different global scope ka `Array` constructor) — `Array.isArray()` is problem ko avoid karta hai, isliye ye zyada reliable hai.
- Functions ke liye `typeof fn === "function"` reliable hai (unlike objects/arrays).

## 12. Real-World Usage

Function parameters validate karte waqt (especially JavaScript mein jaha TypeScript nahi use ho raha), type-checking functions common hain — jaise ek API response ka data array hai ya nahi verify karna before `.map()` call karna.

## 13. Comparison With Related Concepts

| Method | Best For | Limitation |
|--------|----------|------------|
| `typeof` | Primitives, functions | Arrays aur null ke liye misleading |
| `instanceof` | Custom classes, built-in objects | Primitives ke liye kaam nahi karta; cross-context issues |
| `Array.isArray()` | Specifically arrays | Sirf arrays ke liye hai |

## 14. Practice Questions

1. `typeof []` kya return karta hai?
2. Array check karne ka sahi tareeka kya hai?
3. `null` ko check karne ka reliable tareeka likho.

## 15. Challenge

Ek function `describeType(value)` likho jo `"string"`, `"number"`, `"array"`, `"null"`, `"object"`, `"function"`, `"boolean"` correctly identify kare 7 different test values ke liye.

## 16. Interview Questions

**Q1: Array ko check karne ke liye `typeof` kyun use nahi karna chahiye?**
A: Kyunki `typeof` arrays ke liye `"object"` return karta hai, `"array"` nahi — arrays JavaScript mein internally objects hi hain. Isliye array-specific check ke liye `Array.isArray(value)` use karna chahiye, jo purpose-built aur reliable method hai, cross-context scenarios mein bhi.

**Q2: `typeof null` kya return karta hai aur kyun ye ek "bug" mana jaata hai?**
A: `typeof null` `"object"` return karta hai, jabki logically `null` ek separate primitive type honi chahiye thi. Yeh JavaScript ki original 1995 implementation ka bug hai jo backward compatibility ki wajah se kabhi fix nahi kiya gaya. Isliye `null` check karne ke liye `value === null` use karna chahiye, `typeof value === "object"` nahi (jo plain objects se bhi match ho jaayega).

## 17. Chapter Summary

`typeof` primitive types aur functions ke liye reliable hai, lekin arrays aur `null` ke liye misleading results deta hai. `instanceof` classes/constructors check karne ke liye hai, prototype chain traverse karta hai. Arrays ke liye `Array.isArray()`, `null` ke liye `=== null` use karna best practice hai.

## 18. Revision Checklist

- [ ] `typeof` ke sabhi common outputs yaad hain.
- [ ] Array aur null check karne ka correct tareeka yaad hai.
- [ ] `instanceof` ka basic use-case samajh gaya.

---

**Next:** [07-Regular-Expressions.md](./07-Regular-Expressions.md)
