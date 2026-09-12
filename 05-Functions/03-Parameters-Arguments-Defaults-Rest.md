# Parameters, Arguments, Default Parameters, Rest Parameters

**Module:** 05-Functions
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [02-Arrow-Functions.md](./02-Arrow-Functions.md)
**Next:** [04-Callbacks-and-Higher-Order-Functions.md](./04-Callbacks-and-Higher-Order-Functions.md)

---

## 1. Learning Objectives

- Parameters aur arguments ka difference samajhna.
- Default parameters se fallback values set karna.
- Rest parameters se flexible-argument functions banana.
- `arguments` object (legacy) ko samajhna.

## 2. Prerequisites

Modules 05, chapters 01-02.

## 3. Concept in Simple Hinglish

**Parameters** function definition mein diye gaye placeholder naam hain. **Arguments** actual values hain jo function call karte waqt pass ki jaati hain. Default parameters ek fallback value dete hain agar argument diya na jaaye. Rest parameters extra arguments ko array mein collect karte hain.

## 4. Technical Explanation

```js
function greet(name, greeting = "Hello") { // 'name', 'greeting' = parameters; "Hello" = default
  return `${greeting}, ${name}`;
}
greet("Amit"); // "Amit" = argument
```

**Default parameters:** Agar argument `undefined` pass hota hai (ya bilkul nahi diya), default value use hoti hai.

**Rest parameters:** `...args` function ke last parameter mein "remaining" arguments ko array mein collect karta hai (detail [03-Operators/05-Spread-and-Rest.md](../03-Operators/05-Spread-and-Rest.md) mein bhi hai).

**`arguments` object ⚠️ (Legacy):** Regular functions ke andar automatically available, array-like object jo saare passed arguments rakhta hai. Arrow functions mein exist nahi karta.

## 5. Syntax

```js
function fn(a, b = 10, ...rest) {
  console.log(a, b, rest);
}
```

## 6. Basic Examples

```js
function multiply(a, b = 2) {
  return a * b;
}
console.log(multiply(5));     // 10 (b defaults to 2)
console.log(multiply(5, 3));  // 15
```

## 7. Detailed Examples

**Default parameters can reference earlier parameters:**
```js
function calculatePrice(price, tax = price * 0.18) {
  return price + tax;
}
console.log(calculatePrice(1000));      // 1180 (tax auto-calculated)
console.log(calculatePrice(1000, 50));   // 1050 (explicit tax used)
```

**Rest parameters — flexible function signatures:**
```js
function createTeam(captain, ...members) {
  console.log("Captain:", captain);
  console.log("Members:", members);
}
createTeam("Virat", "Rohit", "Rahul", "Jasprit");
// Captain: Virat
// Members: ["Rohit", "Rahul", "Jasprit"]
```

**`arguments` object — legacy way (avoid in new code):**
```js
function oldStyleSum() {
  let total = 0;
  for (let i = 0; i < arguments.length; i++) {
    total += arguments[i];
  }
  return total;
}
console.log(oldStyleSum(1, 2, 3, 4)); // 10
```

**✅ Modern equivalent using rest parameters:**
```js
function modernSum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
console.log(modernSum(1, 2, 3, 4)); // 10
```

`arguments` ek real array nahi hai (array-like object hai) — isliye `.reduce()`, `.map()` jaise array methods directly use nahi ho sakte bina convert kiye (`Array.from(arguments)`). Rest parameters ye problem solve karte hain — `...numbers` ek **real array** hai.

**Missing arguments — undefined vs default:**
```js
function example(a, b) {
  console.log(a, b);
}
example(1); // 1 undefined — missing argument becomes undefined
```

## 8. Mental Model

Parameters ko socho ek **form ke blank fields** ki tarah (Name: ___, Age: ___). Arguments wo **actual data** hai jo tum form mein fill karte ho. Default parameters ek **"pre-filled suggestion"** hain jo field khali chhodne pe automatically use ho jaati hai. Rest parameters ek **"extra notes" box** hai jaha jitne bhi extra items ho, sab collect ho jaate hain.

## 9. What Happens Internally?

Function call hone par engine ek naya scope banata hai jisme parameters ko arguments se **bind** kiya jaata hai left-to-right. Default parameters ka evaluation **call-time** pe hota hai (har call pe fresh evaluate hota hai, agar argument missing hai). Rest parameters ke liye engine bache hue saare arguments ko ek naya array object mein collect karta hai.

## 10. Common Mistakes

- `arguments` object ko arrow function ke andar use karne ki koshish karna — exist nahi karta.
- Default parameter values ko sirf static values samajhna — ye expressions bhi ho sakte hain, aur pichle parameters reference kar sakte hain.
- Rest parameter ko non-last position mein rakhna — syntax error.

## 11. Edge Cases

```js
function test(a = 5, b) {
  console.log(a, b);
}
test(undefined, 10); // 5 10 — undefined triggers default, even if explicitly passed!
test(null, 10);      // null 10 — null does NOT trigger default (only undefined does)
```
Ye important edge case hai — sirf **`undefined`** default value trigger karta hai, `null` nahi.

## 12. Real-World Usage

Default parameters configuration objects ke liye bahut common hain (jaise `function fetchData(url, options = {})`). Rest parameters variadic utility functions (jaise custom `Math.max`, logging functions) banane mein use hote hain.

## 13. Comparison With Related Concepts

| Concept | Purpose |
|---------|---------|
| Default parameters | Fallback value when argument missing/undefined |
| Rest parameters | Collect unlimited extra arguments into array |
| `arguments` object ⚠️ | Legacy array-like access to all arguments (avoid in new code) |

## 14. Practice Questions

1. Default parameter kab trigger hota hai — sirf argument na dene pe ya `undefined` pass karne pe bhi?
2. `arguments` object aur rest parameters mein kya difference hai?
3. Kya default parameter dusre parameter ko reference kar sakta hai?

## 15. Challenge

Ek function `createInvoice(customerName, ...items)` likho jo items (numbers) ka total calculate kare aur ek formatted string return kare: `"Invoice for [name]: Total = [total]"`.

## 16. Interview Questions

**Q1: `arguments` object aur rest parameters (`...args`) mein kya difference hai?**
A: `arguments` ek array-like object hai jo sirf regular functions ke andar available hai (arrow functions mein nahi), aur ye ek real array nahi hai — array methods (`map`, `reduce`) directly use nahi ho sakte. Rest parameters (`...args`) ek **real array** create karte hain, arrow functions mein bhi kaam karte hain, aur explicitly named parameter ki tarah define hote hain — isliye modern JavaScript mein `arguments` ke bajaye rest parameters recommended hain.

**Q2: Default parameter value kab use hoti hai?**
A: Default parameter value tab use hoti hai jab corresponding argument **`undefined`** ho — chahe wo argument bilkul na diya gaya ho, ya explicitly `undefined` pass kiya gaya ho. Important: `null` pass karne se default trigger **nahi** hota, kyunki `null` khud ek valid, defined value hai.

## 17. Chapter Summary

Parameters function definition ke placeholders hain, arguments actual passed values hain. Default parameters missing/undefined arguments ke liye fallback dete hain. Rest parameters (`...args`) unlimited extra arguments ko real array mein collect karte hain — ye legacy `arguments` object se better modern approach hai.

## 18. Revision Checklist

- [ ] Parameter vs argument ka difference clear hai.
- [ ] Default parameter sirf `undefined` pe trigger hota hai, `null` pe nahi — yaad hai.
- [ ] Rest parameters `arguments` object se kaise better hain, samajh gaya.

---

**Next:** [04-Callbacks-and-Higher-Order-Functions.md](./04-Callbacks-and-Higher-Order-Functions.md)
