# Arithmetic and Assignment Operators

**Module:** 03-Operators
**Difficulty:** 🟢 Beginner
**Previous:** [02-Variables-and-Data-Types/08-Date-and-Intl.md](../02-Variables-and-Data-Types/08-Date-and-Intl.md)
**Next:** [02-Comparison-and-Equality.md](./02-Comparison-and-Equality.md)

---

## 1. Learning Objectives

- Sabhi arithmetic operators use karna seekhna.
- Compound assignment operators (`+=`, `-=`, etc.) samajhna.
- Increment/decrement operators ka pre/post behavior clear karna.

## 2. Prerequisites

Module 02 complete.

## 3. Concept in Simple Hinglish

Arithmetic operators wo symbols hain jo numbers pe mathematical operations karte hain — jaise `+` add karta hai, `-` subtract karta hai. Assignment operators values ko variables mein store karte hain, aur compound assignment operators (`+=` jaisa) ek shortcut hote hain "calculate karo aur wapas store karo" ke liye.

## 4. Technical Explanation

**Arithmetic Operators:**

| Operator | Meaning | Example |
|----------|---------|---------|
| `+` | Addition | `5 + 3` → `8` |
| `-` | Subtraction | `5 - 3` → `2` |
| `*` | Multiplication | `5 * 3` → `15` |
| `/` | Division | `10 / 2` → `5` |
| `%` | Modulus (remainder) | `10 % 3` → `1` |
| `**` | Exponentiation | `2 ** 3` → `8` |
| `++` | Increment | `x++` |
| `--` | Decrement | `x--` |

**Assignment Operators:**

| Operator | Equivalent To |
|----------|----------------|
| `x += y` | `x = x + y` |
| `x -= y` | `x = x - y` |
| `x *= y` | `x = x * y` |
| `x /= y` | `x = x / y` |
| `x %= y` | `x = x % y` |
| `x **= y` | `x = x ** y` |

## 5. Syntax

```js
let result = 10 + 5;
let x = 10;
x += 5; // x = 15
```

## 6. Basic Examples

```js
console.log(7 + 3);   // 10
console.log(7 % 2);   // 1
console.log(2 ** 4);  // 16
```

## 7. Detailed Examples

**Pre-increment vs Post-increment — ek common confusion:**
```js
let a = 5;
console.log(a++); // 5 (pehle current value return hoti hai, PHIR increment)
console.log(a);   // 6

let b = 5;
console.log(++b); // 6 (PEHLE increment hota hai, phir value return hoti hai)
console.log(b);   // 6
```

`a++` (post-increment) current value **return karta hai pehle**, phir background mein variable ko increment karta hai. `++b` (pre-increment) **pehle increment** karta hai, phir new value return karta hai.

**Real bug scenario:**
```js
let count = 0;
let messages = [];

for (let i = 0; i < 3; i++) {
  messages.push(`Message ${count++}`);
}

console.log(messages); // ["Message 0", "Message 1", "Message 2"]
console.log(count);    // 3
```

## 8. Mental Model

`x++` ko socho "photo click karo, PHIR aage badho" — current state ka snapshot pehle milta hai. `++x` ko socho "pehle aage badho, PHIR photo click karo" — updated state ka snapshot milta hai.

## 9. What Happens Internally?

Engine expression evaluate karte time increment/decrement operation ko ek temporary value store karta hai (post-increment ke case mein), operation perform karta hai, phir stored temporary value ko return karta hai. Pre-increment mein directly updated value return hoti hai — koi temporary storage step nahi.

## 10. Common Mistakes

- `x++` aur `++x` ko same samajhna jab wo ek expression ke andar use ho rahe hain (jaise `arr[i++]`).
- Modulus operator (`%`) ko "percentage" se confuse karna — ye division ka **remainder** deta hai.
- Floating point precision issues ignore karna: `0.1 + 0.2 === 0.3` → `false` (binary floating-point representation ki wajah se).

## 11. Edge Cases

```js
console.log(0.1 + 0.2); // 0.30000000000000004 — floating point precision issue
console.log(5 / 0);      // Infinity
console.log(-5 / 0);     // -Infinity
console.log(0 / 0);      // NaN
```

## 12. Real-World Usage

Modulus operator (`%`) real projects mein bahut use hota hai — jaise "even/odd check" (`n % 2 === 0`), "pagination" (kis page pe kitne items), circular array indexing (`i % array.length`).

## 13. Comparison With Related Concepts

| Concept | Related To |
|---------|------------|
| `+=`, `-=` etc. | Compact form of reassignment |
| `++`, `--` | Shortcut for `+= 1` / `-= 1` |
| `%` | Different from division `/` — gives remainder, not quotient |

## 14. Practice Questions

1. `10 % 3` ka result kya hoga?
2. `let x = 5; console.log(x++ + ++x);` — predict output.
3. `0.1 + 0.2 === 0.3` — true ya false? Kyun?

## 15. Challenge

Ek function likho `isEven(n)` jo modulus operator use karke check kare number even hai ya odd, bina if-else ke ternary operator use karke.

## 16. Interview Questions

**Q1: `x++` aur `++x` mein kya difference hai?**
A: `x++` (post-increment) current value return karta hai expression mein, phir variable ko increment karta hai — matlab "old value use hoti hai, phir increment hota hai". `++x` (pre-increment) pehle variable ko increment karta hai, phir new (updated) value return karta hai. Standalone statement mein (`x++;` alone) result same hota hai, lekin expression ke andar (`console.log(x++)`) result different hota hai.

**Q2: `0.1 + 0.2` JavaScript mein exactly `0.3` kyun nahi deta?**
A: Kyunki JavaScript numbers ko IEEE 754 double-precision floating-point format mein store karta hai, jo decimal fractions (jaise 0.1) ko exactly represent nahi kar sakta — binary mein ye repeating fractions ban jaate hain, jisse chhota rounding error aata hai. Result `0.30000000000000004` aata hai. Financial calculations mein isliye special libraries ya integer-based math (cents mein calculate karna) use karna chahiye.

## 17. Chapter Summary

Arithmetic operators (`+`, `-`, `*`, `/`, `%`, `**`) basic math operations perform karte hain. Assignment operators (`+=` etc.) reassignment ko shorten karte hain. `++`/`--` ke pre/post forms mein subtle but important difference hai jab expression ke andar use hote hain. Floating-point precision JavaScript numbers ki ek known limitation hai.

## 18. Revision Checklist

- [ ] Sabhi arithmetic operators yaad hain.
- [ ] Pre vs post increment/decrement ka difference clear hai.
- [ ] Floating point precision issue ka basic idea hai.

---

**Next:** [02-Comparison-and-Equality.md](./02-Comparison-and-Equality.md)
