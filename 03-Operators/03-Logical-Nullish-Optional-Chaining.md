# Logical Operators, Nullish Coalescing, Optional Chaining

**Module:** 03-Operators
**Difficulty:** 🟡 Intermediate
**Previous:** [02-Comparison-and-Equality.md](./02-Comparison-and-Equality.md)
**Next:** [04-Ternary-Unary-Bitwise.md](./04-Ternary-Unary-Bitwise.md)

---

## 1. Learning Objectives

- Logical operators (`&&`, `||`, `!`) ka short-circuit behavior samajhna.
- Nullish coalescing (`??`) aur logical OR (`||`) ka difference clear karna.
- Optional chaining (`?.`) se safe property access seekhna.

## 2. Prerequisites

Module 03, chapters 01-02, aur [09-Type-Coercion/01-Truthy-and-Falsy.md](../09-Type-Coercion/01-Truthy-and-Falsy.md) helpful hoga (abhi truthy/falsy ka basic idea kaafi hai).

## 3. Concept in Simple Hinglish

`&&` aur `||` "AND"/"OR" logic ke liye hain, lekin JavaScript mein ye sirf `true`/`false` nahi, actual **values** return karte hain — ye ek powerful pattern hai. `??` (nullish coalescing) sirf `null`/`undefined` ke liye default value deta hai. `?.` (optional chaining) safely nested properties access karta hai bina error diye jab koi beech ki property missing ho.

## 4. Technical Explanation

**Short-circuit evaluation:**
- `&&`: Left side falsy hai to usi ko return kar deta hai (right side evaluate nahi hota). Agar left truthy hai, right side return hota hai.
- `||`: Left side truthy hai to usi ko return kar deta hai. Agar left falsy hai, right side return hota hai.
- `!`: Value ko boolean mein convert karke invert karta hai.

**Nullish coalescing (`??`):** Left side sirf `null` ya `undefined` ho tab right side return karta hai — dusri falsy values (`0`, `""`, `false`) ko "empty" nahi maanta.

**Optional chaining (`?.`):** Agar koi beech ki property `null`/`undefined` hai, poori expression `undefined` return kar deti hai, error throw nahi karti.

## 5. Syntax

```js
a && b
a || b
!a
a ?? b
obj?.property
obj?.method?.()
arr?.[0]
```

## 6. Basic Examples

```js
console.log(true && "Hello");   // "Hello"
console.log(false || "Default"); // "Default"
console.log(!true);              // false

console.log(0 ?? "fallback");     // 0 (0 is not null/undefined)
console.log(0 || "fallback");     // "fallback" (0 is falsy)
```

## 7. Detailed Examples

**`||` vs `??` — ek real bug jo `??` fix karta hai:**
```js
function getVolume(userVolume) {
  return userVolume || 50; // Default volume 50
}

console.log(getVolume(0));   // 50 ⚠️ BUG! User ne 0 (mute) set kiya tha, lekin default aa gaya!
```

`0` falsy hai, isliye `||` ne isse "empty" samajh ke default value use kar li — ye ek real bug hai jab `0`, `""`, ya `false` valid values ho sakti hain.

```js
function getVolumeFixed(userVolume) {
  return userVolume ?? 50; // Only null/undefined triggers default
}

console.log(getVolumeFixed(0));         // 0 ✅ Correct — user's actual choice preserved
console.log(getVolumeFixed(undefined)); // 50 ✅ Default applied correctly
```

**Optional chaining — safe nested access:**
```js
let user = {
  name: "Sara",
  address: { city: "Pune" }
};

console.log(user.address?.city);     // "Pune"
console.log(user.contact?.email);    // undefined (no error, even though 'contact' doesn't exist)
// console.log(user.contact.email);  // ❌ TypeError: Cannot read properties of undefined

console.log(user.getAge?.());        // undefined (method doesn't exist, but no error)
```

**Combining `?.` with `??` — common real-world pattern:**
```js
let city = user.address?.city ?? "City not provided";
console.log(city); // "Pune"

let email = user.contact?.email ?? "Email not provided";
console.log(email); // "Email not provided"
```

## 8. Mental Model

`&&`/`||` ko socho ek **relay race** ki tarah — jaise hi result decide ho jaata hai, baton (evaluation) aage nahi jaati (short-circuit). `?.` ko socho ek **cautious explorer** jo har step pe check karta hai "kya aage rasta hai?" — agar nahi, to turant ruk jaata hai bina crash kiye.

## 9. What Happens Internally?

Engine `&&`/`||` expressions ko left-to-right evaluate karta hai, aur jaise hi final result determine ho jaata hai (short-circuit condition meet ho jaati hai), remaining expression **evaluate hi nahi hota** — ye performance ke liye bhi useful hai (jaise `condition && expensiveFunction()`).

Optional chaining internally ek check insert karta hai — agar chain mein koi value `null`/`undefined` milti hai, poori expression turant `undefined` return kar deti hai, aage ki property access skip ho jaati hai.

## 10. Common Mistakes

- `||` ko default values ke liye use karna jab `0`, `""`, `false` valid values ho sakti hain — `??` use karo.
- Optional chaining ko galat samajhna ki ye errors "silence" karta hai poori tarah — ye sirf `null`/`undefined` access ko safe banata hai, dusre errors (jaise calling a non-function) abhi bhi throw honge.
- `&&` ko complex conditions mein overuse karna jaha readability better `if` se hoti.

## 11. Edge Cases

```js
console.log(null ?? undefined ?? "default"); // "default" — chains left to right
// console.log(null ?? true || false); // ❌ SyntaxError — ?? ko && ya || ke saath directly mix nahi kar sakte bina parentheses
console.log((null ?? true) || false);  // true — parentheses zaroori hain
```

## 12. Real-World Usage

Optional chaining aur nullish coalescing **extremely common** hain modern APIs handle karte waqt — jaise deeply nested JSON response se safely data extract karna: `response?.data?.user?.profile?.avatar ?? "/default-avatar.png"`.

## 13. Comparison With Related Concepts

| Operator | Checks For | Use Case |
|----------|------------|----------|
| `\|\|` | Falsy values (`0`, `""`, `false`, `null`, `undefined`, `NaN`) | General fallback |
| `??` | Only `null`/`undefined` | Default values where 0/""/false are valid |
| `?.` | Safe property/method access | Avoiding TypeErrors on nested access |

## 14. Practice Questions

1. `false || 0 || "" || "final"` — result kya hoga?
2. `0 ?? "default"` aur `0 || "default"` mein result ka difference batao.
3. `user?.profile?.name` ka use kab karna chahiye?

## 15. Challenge

Ek object `settings = { volume: 0, brightness: null }` banao. `??` use karke `volume` aur `brightness` ke liye defaults set karo taaki `volume` apni actual value (0) rakhe, lekin `brightness` ke liye default value aaye.

## 16. Interview Questions

**Q1: `||` aur `??` mein kya difference hai?**
A: `||` (logical OR) left operand ke **falsy** hone par right operand return karta hai — falsy values mein `0`, `""`, `false`, `null`, `undefined`, `NaN` sab shamil hain. `??` (nullish coalescing) sirf `null` ya `undefined` hone par right operand return karta hai — dusri falsy values (jaise `0` ya `""`) ko valid maanta hai aur unhe preserve karta hai. Isliye jab `0` ya empty string ek valid value ho sakti hai, `??` zyada correct choice hai.

**Q2: Optional chaining (`?.`) kya problem solve karta hai?**
A: Deeply nested objects se property access karte waqt, agar beech ka koi level `null`/`undefined` hai, normal dot notation (`obj.a.b.c`) `TypeError` throw karta hai. Optional chaining (`obj?.a?.b?.c`) is chain ko safely handle karta hai — jaise hi koi level `null`/`undefined` milta hai, poori expression `undefined` return kar deti hai, error nahi aata. Ye API responses jaisi unpredictable data structures ke saath kaam karte waqt bahut useful hai.

## 17. Chapter Summary

`&&`/`||` short-circuit evaluation follow karte hain aur actual values return karte hain, boolean nahi. `??` sirf `null`/`undefined` check karta hai, jo `0`/`""`/`false` jaisi valid falsy values ke liye `||` se better default hai. `?.` nested property/method access ko safely handle karta hai bina error throw kiye.

## 18. Revision Checklist

- [ ] Short-circuit evaluation ka concept clear hai.
- [ ] `||` vs `??` ka real difference ek example se yaad hai.
- [ ] Optional chaining syntax (`?.`, `?.()`, `?.[]`) yaad hai.

---

**Next:** [04-Ternary-Unary-Bitwise.md](./04-Ternary-Unary-Bitwise.md)
