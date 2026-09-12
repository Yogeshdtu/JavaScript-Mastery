# Ternary, Unary, and Bitwise Operators

**Module:** 03-Operators
**Difficulty:** 🟡 Intermediate
**Previous:** [03-Logical-Nullish-Optional-Chaining.md](./03-Logical-Nullish-Optional-Chaining.md)
**Next:** [05-Spread-and-Rest.md](./05-Spread-and-Rest.md)

---

## 1. Learning Objectives

- Ternary operator se concise conditional expressions likhna.
- Unary operators (`+`, `-`, `typeof`, `!`) ka use samajhna.
- Bitwise operators ka basic concept aur real-world use-case jaanna.

## 2. Prerequisites

Module 03, chapters 01-03.

## 3. Concept in Simple Hinglish

**Ternary operator** ek shortcut hai `if-else` ka jo ek single line expression mein value return karta hai. **Unary operators** sirf ek operand pe kaam karte hain (jaise `-x`). **Bitwise operators** numbers ko unke binary (0s aur 1s) representation pe operate karte hain — inka use rare hai lekin kuch specific scenarios (flags, performance tricks) mein useful hote hain.

## 4. Technical Explanation

**Ternary:**
```js
condition ? valueIfTrue : valueIfFalse
```

**Common Unary Operators:**

| Operator | Purpose | Example |
|----------|---------|---------|
| `+` | Convert to number | `+"5"` → `5` |
| `-` | Negate / convert to number | `-"5"` → `-5` |
| `!` | Boolean negation | `!true` → `false` |
| `typeof` | Get type | `typeof 5` → `"number"` |
| `void` | Evaluates expression, returns `undefined` | `void 0` → `undefined` |

**Bitwise Operators:**

| Operator | Meaning |
|----------|---------|
| `&` | AND |
| `\|` | OR |
| `^` | XOR |
| `~` | NOT |
| `<<` | Left shift |
| `>>` | Right shift |
| `>>>` | Unsigned right shift |

## 5. Syntax

```js
let result = condition ? "yes" : "no";
let num = +"42";
console.log(5 & 3); // Bitwise AND
```

## 6. Basic Examples

```js
let age = 20;
let type = age >= 18 ? "Adult" : "Minor";
console.log(type); // "Adult"

console.log(+"100");  // 100 (string to number)
console.log(!!"text"); // true (double negation → boolean)
```

## 7. Detailed Examples

**Nested ternary — readability ka trade-off:**
```js
let score = 75;

let grade = score >= 90 ? "A"
          : score >= 75 ? "B"
          : score >= 60 ? "C"
          : "F";

console.log(grade); // "B"
```
Nested ternaries kaam karte hain, lekin 2-3 se zyada levels readability kharab kar dete hain — us case mein `if-else` ya lookup table better hota hai.

**Double negation (`!!`) — quick boolean conversion:**
```js
console.log(!!"hello");  // true
console.log(!!0);        // false
console.log(!!undefined); // false
console.log(!!null);      // false
```
`!!value` value ko boolean mein convert karne ka common idiom hai — pehla `!` truthy/falsy ko invert karta hai, dusra `!` usse wapas correct boolean mein flip kar deta hai.

**Bitwise flags — real-world use case:**
```js
const READ = 1;    // 0001
const WRITE = 2;   // 0010
const EXECUTE = 4; // 0100

let permissions = READ | WRITE; // 0011 → 3

console.log((permissions & READ) !== 0);    // true — has READ permission
console.log((permissions & EXECUTE) !== 0); // false — no EXECUTE permission
```
Ye pattern file permissions systems (jaise Unix `chmod`) mein use hota hai — multiple boolean flags ko ek single number mein compactly store karna.

## 8. Mental Model

Ternary operator ko socho ek **quick fork in the road** — ek line mein decision aur result dono. Bitwise operators ko socho tum numbers ko unki **binary "DNA"** (individual bits) ke level pe manipulate kar rahe ho, jabki normal arithmetic operators unhe "whole numbers" ki tarah treat karte hain.

## 9. What Happens Internally?

Bitwise operations perform karne se pehle, engine number ko 32-bit signed integer mein convert karta hai, operation apply karta hai bit-by-bit, phir result ko wapas JavaScript number mein convert karta hai. Isi wajah se bitwise operators bahut large numbers (32-bit se bahar) ke saath unexpected results de sakte hain.

## 10. Common Mistakes

- Nested ternary ko itna complex bana dena ki code unreadable ho jaaye — 2 levels se zyada avoid karo.
- Bitwise `&`/`|` ko logical `&&`/`||` samajh ke use karna (galti se single `&` likh dena jaha `&&` chahiye tha).
- `~` (bitwise NOT) ko `!` (logical NOT) se confuse karna — completely different operators hain.

## 11. Edge Cases

```js
console.log(~5);      // -6  (bitwise NOT flips bits: -(x+1))
console.log(5 << 1);  // 10  (left shift by 1 = multiply by 2)
console.log(-1 >>> 0); // 4294967295 (unsigned right shift converts negative to large positive)
```

## 12. Real-World Usage

Ternary operators JSX (React) mein bahut common hain conditional rendering ke liye. Bitwise operators low-level performance-critical code, graphics programming, aur permission/flag systems mein use hote hain — daily web development mein rare hote hain.

## 13. Comparison With Related Concepts

| Operator Type | Common Usage Frequency |
|----------------|---------------------------|
| Ternary | Very common (conditional expressions) |
| Unary (`+`, `!`, `typeof`) | Very common (type conversion, negation) |
| Bitwise | Rare (specific low-level use cases) |

## 14. Practice Questions

1. `let x = 5; let result = x > 3 ? "big" : "small";` — result kya hoga?
2. `+"3.14"` ka result aur type kya hoga?
3. `5 & 3` ka binary calculation karke result batao.

## 15. Challenge

Ternary operator use karke ek function likho `getDiscount(isPremiumUser)` jo premium user ke liye 20 return kare, normal user ke liye 5.

## 16. Interview Questions

**Q1: Ternary operator ka use kab avoid karna chahiye?**
A: Jab condition complex ho ya multiple nested ternaries chahiye ho — us case mein `if-else` ya `switch` statement zyada readable hote hain. Ternary sirf simple, single-condition value-selection ke liye best hai (jaise default value set karna ya conditional class name assign karna).

**Q2: `!!value` kis liye use hota hai?**
A: `!!value` value ko explicitly boolean mein convert karne ka common shortcut hai. Pehla `!` value ko invert karta hai as boolean (truthy → false, falsy → true), dusra `!` usse original boolean equivalent mein wapas flip kar deta hai. Ye `Boolean(value)` ka concise alternative hai.

## 17. Chapter Summary

Ternary operator concise conditional expressions ke liye hai, unary operators single operand pe kaam karte hain (type conversion, negation), aur bitwise operators binary-level manipulation ke liye hain — rare but useful specific scenarios mein (flags, permissions).

## 18. Revision Checklist

- [ ] Ternary syntax aur nested ternary ka readability trade-off clear hai.
- [ ] `!!value` ka purpose yaad hai.
- [ ] Bitwise operators ka basic real-world use-case (flags) samajh gaya.

---

**Next:** [05-Spread-and-Rest.md](./05-Spread-and-Rest.md)
