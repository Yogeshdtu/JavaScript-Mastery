# Truthy and Falsy Values

**Module:** 09-Type-Coercion
**Difficulty:** 🟡 Intermediate
**Previous:** [08-Scope-and-Closures/06-call-apply-bind.md](../08-Scope-and-Closures/06-call-apply-bind.md)
**Next:** [02-Implicit-vs-Explicit-Coercion.md](./02-Implicit-vs-Explicit-Coercion.md)

---

## 1. Learning Objectives

- Saari falsy values ki complete list yaad karna.
- Truthy/falsy ka conditional statements mein practical impact samajhna.

## 2. Prerequisites

Modules 01-08 complete.

## 3. Concept in Simple Hinglish

JavaScript mein jab koi value **boolean context** mein use hoti hai (jaise `if` condition), engine usse automatically `true` ya `false` mein convert kar deta hai. Jo values `false` ban jaati hain unhe **falsy** kehte hain, baaki sab **truthy** hain.

## 4. Technical Explanation

**Complete list of Falsy Values (only 8 — memorize this list):**

| Falsy Value | Type |
|-------------|------|
| `false` | boolean |
| `0` | number |
| `-0` | number |
| `0n` | bigint |
| `""` | string (empty) |
| `null` | null |
| `undefined` | undefined |
| `NaN` | number |

**Everything else is truthy** — including `"0"` (string), `[]` (empty array), `{}` (empty object)!

## 5. Syntax

```js
if (value) { /* truthy branch */ }
Boolean(value); // Explicit conversion
```

## 6. Basic Examples

```js
if (0) console.log("won't run");
if ("") console.log("won't run");
if ("hello") console.log("will run"); // ✅
if ([]) console.log("will run");        // ✅ (empty array is truthy!)
if ({}) console.log("will run");        // ✅ (empty object is truthy!)
```

## 7. Detailed Examples

**The classic beginner trap — empty array/object are truthy:**
```js
let cart = [];

if (cart) {
  console.log("Cart exists"); // This runs! [] is truthy
}

if (cart.length) {
  console.log("Cart has items"); // This does NOT run — cart.length is 0 (falsy)
} else {
  console.log("Cart is empty"); // This runs
}
```
Ye ek **bahut common real bug** hai — developers `if (array)` likh dete hain jab actually `if (array.length)` chahiye tha, kyunki empty array khud truthy hoti hai, uski length falsy hoti hai.

**Using truthy/falsy for default values (pre-`??` pattern):**
```js
function greetUser(name) {
  name = name || "Guest"; // If name is falsy (empty string, undefined, etc.), use "Guest"
  console.log(`Hello, ${name}`);
}

greetUser("Aarav"); // "Hello, Aarav"
greetUser("");        // "Hello, Guest" (empty string is falsy)
greetUser();           // "Hello, Guest" (undefined is falsy)
```
Ye pattern Module 03, chapter 03 mein `??` (nullish coalescing) se compare kiya tha — `||` sab falsy values ke liye trigger hota hai, `??` sirf `null`/`undefined` ke liye.

**Truthy/Falsy check table — testing yourself:**
```js
console.log(Boolean("0"));        // true  ⚠️ (non-empty string, even "0")
console.log(Boolean(" "));         // true  ⚠️ (space is a non-empty string)
console.log(Boolean(-1));           // true  (any non-zero number is truthy)
console.log(Boolean(NaN));          // false
console.log(Boolean(Infinity));     // true
console.log(Boolean(function(){})); // true (functions are always truthy)
```

## 8. Mental Model

Falsy values ko socho ek **short "wanted list"** — sirf 8 specific "criminals" (falsy values) hain jo `if` condition ko fail karte hain. Baaki sab automatically "innocent" (truthy) mane jaate hain, chahe wo dikhne mein "empty" (`[]`, `{}`) hi lagen.

## 9. What Happens Internally?

Jab koi value boolean context mein use hoti hai, engine `ToBoolean` abstract operation apply karta hai (ECMAScript spec ka defined algorithm) — ye directly check karta hai ki value un 8 specific falsy values mein se ek hai ya nahi; agar nahi, result `true` hai.

## 10. Common Mistakes

- `if (array)` likhna jab `if (array.length > 0)` chahiye tha.
- `if (object)` likhna jab specific property check karni thi (`if (Object.keys(object).length > 0)`).
- `"0"` (string) ko falsy samajhna — ye actually truthy hai (non-empty string).

## 11. Edge Cases

```js
console.log(Boolean(document.all)); // false in some old browser quirks — historic exception, not relevant in modern code
console.log(Boolean(new Boolean(false))); // true ⚠️ — a Boolean OBJECT (not primitive) is always truthy!
```
`new Boolean(false)` ek **object** hai (primitive nahi), aur sabhi objects truthy hote hain — chahe unke andar `false` value bhi ho. Isi liye `new Boolean()`, `new Number()`, `new String()` wrapper objects avoid karne chahiye.

## 12. Real-World Usage

Truthy/falsy checks form validation (`if (inputValue)`), conditional rendering (React mein `{items.length > 0 && <List />}`), aur default value assignment mein bahut common hain.

## 13. Comparison With Related Concepts

| Concept | Related To |
|---------|------------|
| `if` conditions | Directly use truthy/falsy conversion |
| `||` operator | Returns first truthy operand (or last if all falsy) |
| `??` operator | Only treats `null`/`undefined` as "empty" — stricter than falsy |

## 14. Practice Questions

1. Saari 8 falsy values yaad karke likho.
2. `if ([])` kya `true` branch execute karega ya `false`?
3. `Boolean("0")` ka result kya hoga?

## 15. Challenge

10 different values likho (mix of truthy/falsy) aur bina run kiye predict karo har ek `Boolean()` ke through kya result dega, phir verify karo.

## 16. Interview Questions

**Q1: JavaScript mein falsy values ki complete list batao.**
A: Sirf 8 falsy values hain: `false`, `0`, `-0`, `0n` (BigInt zero), `""` (empty string), `null`, `undefined`, aur `NaN`. Inke bawajood, empty array (`[]`) aur empty object (`{}`) **truthy** hote hain — ye ek common misconception/interview trap hai.

**Q2: `if (array.length)` aur `if (array)` mein kya practical difference hai?**
A: `if (array)` sirf check karta hai ki `array` variable khud truthy hai ya nahi — aur **koi bhi array (empty ho ya na ho) always truthy hota hai**, isliye ye check hamesha `true` branch mein jaayega jab tak `array` `null`/`undefined` na ho. `if (array.length)` actually check karta hai ki array mein **elements hain ya nahi** — empty array ki `length` `0` hai, jo falsy hai. Real-world logic (jaise "cart empty hai ya nahi") ke liye `.length` check use karna chahiye.

## 17. Chapter Summary

JavaScript mein sirf 8 falsy values hain: `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`. Baaki sab truthy hain — including empty arrays/objects, jo ek common source of bugs hai. `Boolean()` explicit conversion aur `if` conditions implicit conversion use karte hain same rules ke saath.

## 18. Revision Checklist

- [ ] Saari 8 falsy values yaad hain.
- [ ] Empty array/object truthy hote hain, ye clear hai.
- [ ] `array.length` check ka real-world importance samajh gaya.

---

**Next:** [02-Implicit-vs-Explicit-Coercion.md](./02-Implicit-vs-Explicit-Coercion.md)
