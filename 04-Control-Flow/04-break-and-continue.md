# break and continue

**Module:** 04-Control-Flow
**Difficulty:** 🟢 Beginner
**Previous:** [03-for-of-and-for-in.md](./03-for-of-and-for-in.md)
**Next:** [05-Functions/01-Function-Basics.md](../05-Functions/01-Function-Basics.md)

---

## 1. Learning Objectives

- `break` se loop ko completely stop karna seekhna.
- `continue` se current iteration skip karke aage badhna seekhna.
- Labeled statements se nested loops control karna.

## 2. Prerequisites

Module 04, chapters 01-03.

## 3. Concept in Simple Hinglish

`break` loop ko **poori tarah rok deta hai** — jaise ek emergency stop button. `continue` sirf **current iteration ko skip** karta hai aur loop ko aage chalne deta hai — jaise "ye ek step skip karo, baaki chalte raho".

## 4. Technical Explanation

- **`break`:** Loop (ya `switch`) se immediately exit kar jaata hai, code execution loop ke baad wali line se continue hoti hai.
- **`continue`:** Current iteration ko turant khatam kar deta hai aur loop ki next iteration shuru kar deta hai (update step chalega, condition dobara check hogi).

## 5. Syntax

```js
for (let i = 0; i < 10; i++) {
  if (condition) break;
  if (otherCondition) continue;
}
```

## 6. Basic Examples

```js
for (let i = 1; i <= 10; i++) {
  if (i === 5) break;
  console.log(i); // 1 2 3 4
}

for (let i = 1; i <= 5; i++) {
  if (i === 3) continue;
  console.log(i); // 1 2 4 5 (3 skipped)
}
```

## 7. Detailed Examples

**Real-world use — finding first match:**
```js
const users = [
  { id: 1, name: "Amit" },
  { id: 2, name: "Priya" },
  { id: 3, name: "Rahul" }
];

let foundUser = null;

for (const user of users) {
  if (user.name === "Priya") {
    foundUser = user;
    break; // No need to check remaining users
  }
}

console.log(foundUser); // { id: 2, name: "Priya" }
```

**Labeled statements — controlling nested loops:**
```js
outerLoop: for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    if (j === 2) continue outerLoop; // Skips to next iteration of OUTER loop
    console.log(`i=${i}, j=${j}`);
  }
}
// i=1, j=1
// i=2, j=1
// i=3, j=1
```

Normally `continue` sirf **innermost** loop ko affect karta hai. Label (`outerLoop:`) use karke tum specifically outer loop ko continue/break kar sakte ho — ye advanced but useful technique hai jab deeply nested loops control karni ho.

```js
searchLoop: for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    if (i === 1 && j === 1) {
      break searchLoop; // Breaks OUT of both loops entirely
    }
    console.log(i, j);
  }
}
// 0 0
// 0 1
// 0 2
// 1 0
// (stops here — breaks entirely out of both loops)
```

## 8. Mental Model

`break` ko socho ek **fire alarm** — sab kaam turant band ho jaata hai, building (loop) se bahar nikal jaate ho. `continue` ko socho ek **"skip this song"** button on a music player — current item skip hota hai, playlist (loop) chalti rehti hai.

## 9. What Happens Internally?

`break` engine ko batata hai "loop ka control-flow structure yahi khatam karo, aage wali line pe jao". `continue` engine ko batata hai "is iteration ka baaki code skip karo, seedha update step (jaise `i++`) pe jao aur condition dobara check karo".

## 10. Common Mistakes

- `break`/`continue` ko `forEach()` ke andar use karne ki koshish karna — ye kaam nahi karega, kyunki `forEach` ek regular loop nahi hai, callback function hai. Isके liye `for`/`for...of` use karo.
- Labeled statements ko overuse karna — sirf genuinely complex nested-loop scenarios mein use karo, warna code confusing ho jaata hai.

## 11. Edge Cases

```js
[1, 2, 3].forEach((num) => {
  if (num === 2) continue; // ❌ SyntaxError: Illegal continue statement
});
```
`forEach` ke andar `break`/`continue` **syntax error** dete hain kyunki `forEach` ek function callback hai, loop statement nahi. Agar early-exit chahiye array iteration mein, `for...of` ya `.some()`/`.every()` use karo.

## 12. Real-World Usage

`break` search operations mein bahut common hai (jaise linear search — match milte hi rukna). `continue` data filtering/validation loops mein use hota hai (jaise invalid entries skip karke baaki process karna).

## 13. Comparison With Related Concepts

| Statement | Effect |
|-----------|--------|
| `break` | Exits the entire loop (or switch) |
| `continue` | Skips to next iteration |
| `return` (in function) | Exits the whole function, not just the loop |

## 14. Practice Questions

1. `continue` aur `break` mein kya difference hai?
2. Kya `forEach()` ke andar `break` use kar sakte hain?
3. Labeled `continue outerLoop` ka kya effect hota hai nested loop mein?

## 15. Challenge

Ek array `numbers = [4, 7, 2, 9, 1, 15, 3]` diya hai. `for...of` aur `break` use karke pehla number dhundo jo 10 se bada ho, aur usse print karo.

## 16. Interview Questions

**Q1: `break` aur `continue` mein kya difference hai?**
A: `break` loop ki **poori execution ko terminate** kar deta hai — control loop ke immediately baad wali statement pe chala jaata hai. `continue` sirf **current iteration ko skip** karta hai — loop ka update step chalta hai aur condition dobara check hoti hai, loop terminate nahi hota. `break` complete stop hai, `continue` sirf ek iteration ka skip hai.

**Q2: Kya `Array.prototype.forEach()` ke andar `break` ya `continue` use kiya ja sakta hai?**
A: Nahi. `forEach()` ek regular loop nahi hai — ye har element ke liye ek callback function call karta hai. `break`/`continue` sirf actual loop statements (`for`, `while`, `do-while`) ke andar valid hain, function callbacks ke andar nahi — use karne ki koshish `SyntaxError` dega. Agar early exit chahiye, `for...of` loop, ya `.some()`/`.every()` array methods use karna chahiye.

## 17. Chapter Summary

`break` loop ko completely stop kar deta hai, `continue` sirf current iteration skip karke aage badhta hai. Labeled statements (`outerLoop: for...`) nested loops mein specific loop ko target karne ke liye use hote hain. `forEach()` ke andar dono statements invalid hain.

## 18. Revision Checklist

- [ ] `break` vs `continue` ka exact difference clear hai.
- [ ] Labeled statements ka basic use-case samajh gaya.
- [ ] `forEach` ke andar `break`/`continue` na chalne ka reason yaad hai.

---

**Module 04 Complete!** Next Module: [05-Functions/01-Function-Basics.md](../05-Functions/01-Function-Basics.md)
