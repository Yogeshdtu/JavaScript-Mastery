# Loops — for, while, do-while

**Module:** 04-Control-Flow
**Difficulty:** 🟢 Beginner
**Previous:** [01-If-Else-Switch.md](./01-If-Else-Switch.md)
**Next:** [03-for-of-and-for-in.md](./03-for-of-and-for-in.md)

---

## 1. Learning Objectives

- `for`, `while`, aur `do-while` loops likhna aur unka difference samajhna.
- Infinite loops avoid karna seekhna.
- Nested loops ka concept samajhna.

## 2. Prerequisites

Module 04, chapter 01.

## 3. Concept in Simple Hinglish

Loops humein ek hi code ko **baar-baar repeat** karne dete hain bina usse manually har baar likhe. `for` loop use hota hai jab pata ho kitni baar repeat karna hai. `while` loop use hota hai jab condition check karte rehna ho jab tak wo true hai. `do-while` `while` jaisa hai, but pehle ek baar zaroor chalta hai, condition check baad mein hota hai.

## 4. Technical Explanation

```js
for (initialization; condition; update) {
  // code
}

while (condition) {
  // code
}

do {
  // code
} while (condition);
```

**`for` loop** ke 3 parts: initialization (ek baar), condition (har iteration check), update (har iteration ke end mein).

**`while`** sirf condition check karta hai — agar shuru mein hi false hai, loop **kabhi nahi chalega**.

**`do-while`** body pehle **execute** karta hai, phir condition check karta hai — isliye minimum **ek baar zaroor chalta hai**, chahe condition false ho.

## 5. Syntax

```js
for (let i = 0; i < 5; i++) {
  console.log(i);
}

let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

let j = 0;
do {
  console.log(j);
  j++;
} while (j < 5);
```

## 6. Basic Examples

```js
for (let i = 1; i <= 5; i++) {
  console.log(i); // 1 2 3 4 5
}
```

## 7. Detailed Examples

**`while` vs `do-while` — key difference dikhane wala example:**
```js
let count = 10;

while (count < 5) {
  console.log("while:", count); // Never runs — condition false from start
  count++;
}

do {
  console.log("do-while:", count); // Runs once! Prints "do-while: 10"
  count++;
} while (count < 5);
```

**Nested loops — multiplication table:**
```js
for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    console.log(`${i} x ${j} = ${i * j}`);
  }
}
// 1 x 1 = 1
// 1 x 2 = 2
// 1 x 3 = 3
// 2 x 1 = 2
// ... and so on
```
Outer loop ek baar chalta hai to inner loop poori tarah complete hota hai — is example mein outer loop 3 baar chalega, aur har baar inner loop 3 baar chalega — total 9 iterations.

**Infinite loop — common bug (avoid running this!):**
```js
// ⚠️ DANGER: Ye code browser/Node.js ko freeze kar sakta hai
// let i = 0;
// while (i < 5) {
//   console.log(i);
//   // i++ bhool gaye — i kabhi 5 nahi banega, loop infinite chalega
// }
```
Update step bhool jaana (jaise `i++` na likhna) sabse common infinite-loop bug hai.

## 8. Mental Model

`for` loop ko socho ek **counter machine** jisme shuru se pata hai kitni baar chalna hai. `while` ko socho ek **security guard** jo bas condition check karta rehta hai jab tak wo true hai. `do-while` ek **"pehle try karo, phir decide karo dobara try karna hai ya nahi"** wala approach hai.

## 9. What Happens Internally?

Har iteration mein engine condition ko evaluate karta hai — agar `true` hai, loop body execute hoti hai, phir update step chalta hai, phir wapas condition check hoti hai. Yeh cycle chalta rehta hai jab tak condition `false` na ho jaaye. Har check aur update ek chhota overhead hai — bahut large loops mein performance consideration bhi aata hai (Module 22 - Performance).

## 10. Common Mistakes

- Update step (`i++`) bhool jaana — infinite loop.
- Loop condition mein galat operator use karna (`<=` vs `<`), jisse "off-by-one" errors aate hain.
- `while` aur `do-while` ko interchangeably samajhna — jab minimum ek execution guarantee chahiye, `do-while` use karo.

## 11. Edge Cases

```js
for (let i = 0; i < 5; i++) {
  if (i === 3) continue; // Skip iteration 3
  console.log(i); // 0 1 2 4
}
```

## 12. Real-World Usage

`for` loops arrays process karne mein use hote hain (though modern code often `map`/`forEach` prefer karta hai — Module 06). `while` loops tab useful hain jab exact iteration count pehle se pata na ho — jaise "user se retry input lena jab tak valid input na mile", ya "queue empty hone tak process karna".

## 13. Comparison With Related Concepts

| Loop Type | Best For | Guarantees at Least 1 Run? |
|-----------|----------|------------------------------|
| `for` | Known iteration count | No |
| `while` | Unknown iteration count, condition-based | No |
| `do-while` | Need guaranteed first execution | Yes |

## 14. Practice Questions

1. `do-while` loop `while` loop se kaise different hai?
2. Ek infinite loop kaise create hota hai (accidentally)?
3. Nested loop mein outer aur inner loop ka execution order kaise kaam karta hai?

## 15. Challenge

Ek program likho jo 1 se 20 tak sabhi numbers print kare, lekin sirf woh numbers jo 3 se divisible hain (use `for` loop with `continue` or `if`).

## 16. Interview Questions

**Q1: `while` aur `do-while` loop mein kya difference hai?**
A: `while` loop condition ko **pehle check** karta hai, phir body execute karta hai — agar condition initially false hai, body kabhi nahi chalega. `do-while` loop body ko **pehle execute** karta hai, phir condition check karta hai — isliye body kam se kam **ek baar** zaroor chalta hai, chahe condition initially false ho. `do-while` useful hai jab minimum-one-execution guarantee chahiye (jaise menu display karna user input lene se pehle).

**Q2: Infinite loop kaise avoid kiya jaata hai?**
A: Infinite loop avoid karne ke liye ensure karo ki loop ka update step (`i++` jaisa) sahi se likha ho aur eventually condition ko `false` bana de. `while`/`do-while` mein especially dhyaan rakhna zaroori hai ki loop body ke andar condition-affecting variable ko update kiya jaaye. Debugging ke time, agar browser/terminal freeze ho jaaye, ye check karna chahiye ki koi loop condition kabhi false nahi ban rahi.

## 17. Chapter Summary

`for` loop known iteration count ke liye best hai, `while` condition-based repetition ke liye hai, aur `do-while` guarantee karta hai minimum ek execution. Update step bhool jaana infinite loops ka sabse common cause hai.

## 18. Revision Checklist

- [ ] Teeno loop types ka syntax yaad hai.
- [ ] `while` vs `do-while` ka key difference clear hai.
- [ ] Infinite loop avoid karne ka tareeka samajh gaya.

---

**Next:** [03-for-of-and-for-in.md](./03-for-of-and-for-in.md)
