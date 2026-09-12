# If-Else and Switch

**Module:** 04-Control-Flow
**Difficulty:** 🟢 Beginner
**Previous:** [03-Operators/06-typeof-instanceof-in-delete.md](../03-Operators/06-typeof-instanceof-in-delete.md)
**Next:** [02-Loops-for-while-dowhile.md](./02-Loops-for-while-dowhile.md)

---

## 1. Learning Objectives

- `if`, `else if`, `else` se conditional logic likhna.
- `switch` statement ka correct use aur `break`/fall-through samajhna.
- Kab `if-else` aur kab `switch` use karna, decide kar paana.

## 2. Prerequisites

Module 03 complete.

## 3. Concept in Simple Hinglish

Control flow statements decide karte hain ki code ka **kaunsa part chalega** based on conditions. `if-else` sabse basic decision-making tool hai — "agar ye condition true hai, ye karo, warna wo karo". `switch` ek alternative hai jab **ek hi value** ko multiple specific cases se compare karna ho.

## 4. Technical Explanation

```js
if (condition1) {
  // block A
} else if (condition2) {
  // block B
} else {
  // block C
}
```

`switch` ek value ko multiple `case` values se **strict equality (`===`)** ke saath compare karta hai:
```js
switch (value) {
  case option1:
    // code
    break;
  case option2:
    // code
    break;
  default:
    // code
}
```

## 5. Syntax

```js
if (age >= 18) {
  console.log("Adult");
} else {
  console.log("Minor");
}

switch (day) {
  case "Mon":
    console.log("Monday");
    break;
  default:
    console.log("Unknown");
}
```

## 6. Basic Examples

```js
let temperature = 30;

if (temperature > 25) {
  console.log("It's hot");
} else {
  console.log("It's pleasant");
}
```

## 7. Detailed Examples

**Switch fall-through — ek important gotcha:**
```js
let fruit = "apple";

switch (fruit) {
  case "apple":
    console.log("Apple selected");
  case "banana": // ⚠️ No break above! Execution "falls through" here
    console.log("Banana selected");
    break;
  default:
    console.log("Unknown fruit");
}
// Output:
// Apple selected
// Banana selected
```
`break` na hone ki wajah se, jab `"apple"` case match hua, execution neeche wale case mein bhi "fall through" ho gaya jab tak `break` nahi mila. Ye intentional bhi use hota hai (multiple cases ka same code), lekin accidental bhi ho sakta hai — isi liye `switch` mein `break` bhoolna ek common bug source hai.

**Intentional fall-through — grouped cases:**
```js
function getSeason(month) {
  switch (month) {
    case "Dec":
    case "Jan":
    case "Feb":
      return "Winter";
    case "Mar":
    case "Apr":
    case "May":
      return "Summer";
    default:
      return "Unknown";
  }
}
console.log(getSeason("Jan")); // "Winter"
```

**Switch ki strict comparison — type matters:**
```js
let value = "5";
switch (value) {
  case 5: // number 5 — won't match!
    console.log("Matched number");
    break;
  case "5": // string "5" — matches
    console.log("Matched string");
    break;
}
// Output: Matched string
```

## 8. Mental Model

`if-else` ko socho ek **flowchart ka decision diamond** — har condition ek fork hai. `switch` ko socho ek **vending machine** — tum ek specific button (value) press karte ho, machine directly wahi specific slot (case) select kar leta hai, ek-ek condition check karne ke bajaye.

## 9. What Happens Internally?

Engine `if` conditions ko **top-to-bottom sequentially** evaluate karta hai jab tak koi true na mile. `switch` bhi conceptually sequentially cases check karta hai (strict equality se), lekin kuch engines optimize kar dete hain jump-table jaisi structures se jab cases simple constants hon — performance difference practically negligible hai chhoti switch statements mein.

## 10. Common Mistakes

- `switch` mein `break` bhool jaana — unintended fall-through.
- Bahut zyada nested `if-else` likhna jab `switch` ya lookup object zyada readable hota.
- `switch` mein `==` jaisi loose comparison expect karna — `switch` hamesha `===` (strict) use karta hai.

## 11. Edge Cases

```js
switch (true) {
  case (5 > 3):
    console.log("5 is greater"); // Ye chalega — 'switch(true)' pattern range checks ke liye useful hai
    break;
}
```
Ye "switch(true)" pattern range-based conditions ke liye use hota hai jab exact value match nahi, condition match karni ho.

## 12. Real-World Usage

`if-else` chains form validation, permission checks, aur general branching logic mein sabse common hain. `switch` UI state management (jaise Redux reducers) mein bahut common hai jaha "action type" ke basis pe different logic chalta hai.

## 13. Comparison With Related Concepts

| Aspect | if-else | switch |
|--------|---------|--------|
| Best for | Range/complex conditions | Single value, multiple exact matches |
| Comparison type | Any (`<`, `>`, `===`, etc.) | Strict equality (`===`) only |
| Fall-through risk | No | Yes (if `break` missing) |

## 14. Practice Questions

1. `switch` mein `break` na likhne pe kya hota hai?
2. `switch` case comparison `==` ya `===` use karta hai?
3. `switch(true)` pattern ka use-case kya hai?

## 15. Challenge

Ek function `getGrade(score)` likho jo score ke basis pe grade return kare using `if-else if-else` chain: 90+ "A", 75-89 "B", 60-74 "C", <60 "F". Phir same logic ko `switch(true)` pattern se implement karo.

## 16. Interview Questions

**Q1: `switch` statement mein `break` kyun zaroori hai?**
A: `switch` statement, ek case match hone ke baad, agla code sequentially execute karta rehta hai jab tak `break` na mile — chahe agle cases match kar rahe ho ya nahi ("fall-through" behavior). `break` execution ko `switch` block se bahar nikal deta hai us matched case ke baad. Bina `break`, unintended cases bhi execute ho sakte hain, jo bugs create karta hai.

**Q2: `switch` statement `==` ya `===` use karta hai comparison ke liye?**
A: `switch` hamesha **strict equality (`===`)** use karta hai case values compare karne ke liye — koi type coercion nahi hoti. Isi liye `case 5` (number) `"5"` (string) se match nahi karega.

## 17. Chapter Summary

`if-else` general-purpose conditional branching ke liye hai, `switch` ek single value ko multiple exact matches se compare karne ke liye better readability deta hai. `switch` strict equality use karta hai aur `break` na hone se fall-through hota hai — intentionally ya accidentally.

## 18. Revision Checklist

- [ ] `if-else if-else` chain likh sakta hoon.
- [ ] `switch` ka fall-through behavior aur `break` ki importance clear hai.
- [ ] Kab `switch` aur kab `if-else` use karna, decide kar sakta hoon.

---

**Next:** [02-Loops-for-while-dowhile.md](./02-Loops-for-while-dowhile.md)
