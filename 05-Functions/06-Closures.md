# Closures

**Module:** 05-Functions
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [05-Recursion.md](./05-Recursion.md)
**Next:** [07-IIFE-Composition-Pure-Functions.md](./07-IIFE-Composition-Pure-Functions.md)

---

## 1. Learning Objectives

- Closure ka exact concept samajhna — function apne outer scope ki variables ko kaise "yaad" rakhta hai.
- Closures ke practical applications samajhna (data privacy, function factories).
- Loop ke andar closures ka classic bug aur uska fix samajhna.

## 2. Prerequisites

Module 05, chapters 01-05. [08-Scope-and-Closures/02-Lexical-Scope-and-Scope-Chain.md](../08-Scope-and-Closures/02-Lexical-Scope-and-Scope-Chain.md) padhne se pehle helpful, but ye chapter khud-se-complete hai.

## 3. Concept in Simple Hinglish

Closure ek aisi cheez hai jab ek **inner function apne outer function ki variables ko yaad rakhta hai**, chahe outer function ka execution khatam ho gaya ho. Jaise ek bacha apne parents ke ghar ka address yaad rakhta hai, chahe wo ghar chhod ke kahi aur chala jaaye — wo address (variable access) uske saath rehta hai.

## 4. Technical Explanation

**Closure** = A function bundled together with references to its surrounding **lexical environment**. Jab ek function dusre function ke andar define hota hai, inner function outer function ke variables ko access kar sakta hai — aur ye access **outer function khatam hone ke baad bhi bana rehta hai**, jab tak inner function ka reference kahi exist karta hai.

Closures **JavaScript engine automatically create karta hai** — tumhe explicitly kuch "declare" nahi karna padta. Har function apne creation ke time apne surrounding scope ko "capture" kar leta hai.

## 5. Syntax

```js
function outer() {
  let outerVar = "I am from outer";

  function inner() {
    console.log(outerVar); // inner has access to outerVar — this is closure
  }

  return inner;
}

const myClosure = outer();
myClosure(); // "I am from outer" — works even though outer() has finished executing!
```

## 6. Basic Examples

```js
function makeCounter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}

const counter = makeCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```
Har call pe `count` apni previous value "yaad" rakhta hai — ye closure ki wajah se hai. Normally, function khatam hone ke baad uski local variables garbage-collected ho jaati hain, lekin yahan `count` ka reference `inner function` ke through zinda hai.

## 7. Detailed Examples

**Data privacy — closures se "private" variables banana:**
```js
function createBankAccount(initialBalance) {
  let balance = initialBalance; // 'balance' is private — no direct outside access

  return {
    deposit(amount) {
      balance += amount;
      return balance;
    },
    withdraw(amount) {
      if (amount > balance) {
        return "Insufficient funds";
      }
      balance -= amount;
      return balance;
    },
    getBalance() {
      return balance;
    }
  };
}

const account = createBankAccount(1000);
console.log(account.deposit(500));   // 1500
console.log(account.withdraw(200));  // 1300
console.log(account.balance);         // undefined — no direct access!
console.log(account.getBalance());    // 1300 — only through the exposed method
```
Ye pattern "module pattern" ka foundation hai — `balance` variable ko directly access nahi kiya ja sakta, sirf exposed methods (`deposit`, `withdraw`, `getBalance`) ke through hi interact kiya ja sakta hai. Ye encapsulation/data-privacy achieve karta hai bina classes ke bhi.

**The classic closure-in-loop bug (with `var`):**
```js
function createFunctions() {
  var functions = [];
  for (var i = 0; i < 3; i++) {
    functions.push(function () {
      console.log(i);
    });
  }
  return functions;
}

const fns = createFunctions();
fns[0](); // 3 ⚠️
fns[1](); // 3 ⚠️
fns[2](); // 3 ⚠️
```
**Kyun 3, 3, 3?** `var` function-scoped hai, isliye **saare closures same `i` variable ko share karte hain**. Loop khatam hone tak `i` ki final value `3` ho jaati hai, aur jab functions baad mein call hote hain, sab ko wahi final value milti hai.

**✅ The fix — using `let` (block-scoped):**
```js
function createFunctionsFixed() {
  var functions = [];
  for (let i = 0; i < 3; i++) { // 'let' creates a NEW binding each iteration
    functions.push(function () {
      console.log(i);
    });
  }
  return functions;
}

const fixedFns = createFunctionsFixed();
fixedFns[0](); // 0 ✅
fixedFns[1](); // 1 ✅
fixedFns[2](); // 2 ✅
```
`let` har loop iteration mein ek **naya binding** create karta hai — isliye har closure apna alag `i` capture karta hai, `var` ke opposite jo sirf ek shared binding rakhta hai.

## 8. Mental Model

Closure ko socho ek **backpack** ki tarah jo function apne saath carry karta hai — jab function create hota hai, wo apne surrounding scope ki variables ko backpack mein daal leta hai. Jaha bhi function jaaye (call ho), backpack uske saath rehta hai — wo variables kabhi "khoti" nahi jab tak function khud exist karta hai.

## 9. What Happens Internally?

Jab `outer()` function call hoti hai, ek Execution Context banta hai jisme `outerVar` store hota hai. Normally `outer()` return hone ke baad ye context garbage-collected ho jaata. Lekin agar `inner` function (jo `outerVar` reference karta hai) **bahar return ho jaata hai** (ya kahi store ho jaata hai), engine us specific outer scope ko memory mein zinda rakhta hai — isi ko closure kehte hain. Sirf wahi variables zinda rehte hain jo actually reference ki gayi hain (modern engines optimize karte hain), poora outer scope nahi.

## 10. Common Mistakes

- Loops mein `var` use karna jab har iteration ka apna value capture karna ho — `let` use karo.
- Closures ka overuse karna memory-heavy scenarios mein — bahut saare closures bahut saari variables ko zinda rakh sakte hain, memory leaks ka risk.
- Ye samajhna ki closure sirf `return`ed function tak limited hai — actually koi bhi reference (event listener, timer callback, array mein store kiya function) closure create kar sakta hai.

## 11. Edge Cases

```js
function createMultipleClosures() {
  let sharedValue = 0;
  return {
    increment: () => ++sharedValue,
    decrement: () => --sharedValue
  };
}

const ops = createMultipleClosures();
console.log(ops.increment()); // 1
console.log(ops.increment()); // 2
console.log(ops.decrement()); // 1
```
Multiple functions **same closure scope share** kar sakte hain — dono `increment`/`decrement` same `sharedValue` variable ko modify karte hain.

## 12. Real-World Usage

Closures React Hooks (`useState`, `useEffect`) ke internal implementation mein use hote hain, module patterns mein (data encapsulation), memoization/caching functions mein, aur event handlers mein state maintain karne ke liye (jaise debounce/throttle — Module 22).

## 13. Comparison With Related Concepts

| Concept | Relation to Closures |
|---------|------------------------|
| Scope chain | Closures ka mechanism scope chain ke through kaam karta hai |
| `this` | Closures se alag concept — closures variables capture karte hain, `this` alag rules follow karta hai |
| Module pattern | Closures ka ek direct practical application |

## 14. Practice Questions

1. Closure ki apni definition ek line mein likho.
2. Loop mein `var` use karne se closures kyun galat value capture karte hain?
3. Closures ka use karke data privacy kaise achieve hoti hai?

## 15. Challenge

Ek function `createToggle()` likho jo closure use karke ek boolean state maintain kare, aur har call pe usse flip karke return kare (`true`, `false`, `true`, `false`, ...).

## 16. Interview Questions

**Q1: Closure kya hai? Ek practical example do.**
A: Closure ek function hai jo apne surrounding (lexical) scope ki variables ko "yaad" rakhta hai, chahe outer function ka execution complete ho gaya ho. Practical example: ek counter function jo `let count = 0` ko closure ke through persist karta hai across multiple calls — `function makeCounter() { let count = 0; return () => ++count; }`. Har call `count` ki last value se continue karta hai, kyunki inner function ne outer scope ko "capture" kiya hai.

**Q2: Loop ke andar `var` ke saath closures kyun galat value capture karte hain, aur `let` ye kaise fix karta hai?**
A: `var` function-scoped hoti hai, isliye poore loop ke saare iterations **ek hi shared variable** use karte hain. Jab closures baad mein call hote hain, unhe us variable ki **final** value milti hai (loop khatam hone ke baad wali). `let` block-scoped hai — har loop iteration mein ek **naya, independent binding** create hota hai, isliye har closure apna khud ka snapshot value capture karta hai, jo expected behavior deta hai.

## 17. Chapter Summary

Closure tab banta hai jab function apne outer scope ki variables ko access karta rehta hai, us scope ke "khatam" hone ke baad bhi. Ye data privacy (module pattern), function factories, aur stateful callbacks ke liye powerful hai. Loops mein `var` ke saath closures ek classic bug create karte hain jo `let` se fix hota hai.

## 18. Revision Checklist

- [ ] Closure ki definition aur mechanism clear hai.
- [ ] Data-privacy pattern (bank account example) samajh gaya.
- [ ] `var` vs `let` loop-closure bug aur fix yaad hai.

---

**Next:** [07-IIFE-Composition-Pure-Functions.md](./07-IIFE-Composition-Pure-Functions.md)
