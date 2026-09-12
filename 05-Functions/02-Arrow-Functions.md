# Arrow Functions

**Module:** 05-Functions
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [01-Function-Basics.md](./01-Function-Basics.md)
**Next:** [03-Parameters-Arguments-Defaults-Rest.md](./03-Parameters-Arguments-Defaults-Rest.md)

---

## 1. Learning Objectives

- Arrow function syntax likhna (all variations).
- Arrow functions aur regular functions ke behavioral differences samajhna (especially `this`).
- Kab arrow functions use karna appropriate hai, samajhna.

## 2. Prerequisites

[01-Function-Basics.md](./01-Function-Basics.md)

## 3. Concept in Simple Hinglish

Arrow functions ek **concise (short) syntax** hai function likhne ka, ES6 mein introduced hua. Ye sirf shorthand nahi hai — inka `this` behavior bhi regular functions se **fundamentally different** hai, jo inhe kuch scenarios mein perfect aur kuch mein problematic banata hai.

## 4. Technical Explanation

Arrow functions:
- **No own `this`** — apna `this` create nahi karte, surrounding (lexical) scope se `this` "inherit" karte hain.
- **No `arguments` object** — rest parameters (`...args`) use karna padta hai.
- **Cannot be used as constructors** — `new ArrowFn()` error dega.
- **No implicit return in single-expression form** — automatically expression ka result return karte hain (without curly braces).

## 5. Syntax

```js
const add = (a, b) => a + b;               // implicit return
const square = (n) => { return n * n; };    // explicit return with braces
const greet = name => `Hello, ${name}`;      // single param, no parentheses needed
const noArgs = () => "No arguments";          // no params needs ()
const returnObject = () => ({ key: "value" }); // returning object literal needs parentheses
```

## 6. Basic Examples

```js
const double = (n) => n * 2;
console.log(double(5)); // 10

const numbers = [1, 2, 3];
const doubled = numbers.map((n) => n * 2);
console.log(doubled); // [2, 4, 6]
```

## 7. Detailed Examples

**The `this` difference — the most important distinction:**
```js
const person = {
  name: "Ankit",
  regularGreet: function () {
    console.log(`Regular: Hello, ${this.name}`);
  },
  arrowGreet: () => {
    console.log(`Arrow: Hello, ${this.name}`);
  }
};

person.regularGreet(); // "Regular: Hello, Ankit" — 'this' is the object (person)
person.arrowGreet();   // "Arrow: Hello, undefined" — 'this' is from outer (module/global) scope
```

Arrow function ka `this` object method ke context se nahi aata — ye **jaha function DEFINE hua (lexically)** wahi ka `this` use karta hai, jo yaha global/module scope hai, isliye `this.name` `undefined` hai.

**Practical benefit — arrow functions in callbacks/timers:**
```js
function Timer() {
  this.seconds = 0;

  setInterval(() => {
    this.seconds++; // Arrow function — 'this' correctly refers to Timer instance
    console.log(this.seconds);
  }, 1000);
}

// Compare with regular function (would break):
function BrokenTimer() {
  this.seconds = 0;

  setInterval(function () {
    this.seconds++; // ⚠️ 'this' here is NOT the BrokenTimer instance (undefined or global)
    console.log(this.seconds); // NaN or error
  }, 1000);
}
```
Yahi wajah hai arrow functions callbacks (especially timers, array methods, event handlers jaha outer `this` preserve karni ho) mein bahut popular hain.

**Object methods mein arrow functions avoid karna:**
```js
const counter = {
  count: 0,
  increment: () => {
    this.count++; // ❌ 'this' is NOT 'counter' here
  }
};
```
Object methods ke liye **regular functions** (ya method shorthand `increment() {}`) use karo, arrow functions nahi — warna `this` galat scope se aayega.

## 8. Mental Model

Regular function ko socho ek **naye ghar mein shift hona** — apna naya `this` (context) milta hai jaha bhi call ho. Arrow function ko socho **apne parents ke ghar mein hi rehna** — kabhi apna khud ka `this` nahi banta, jo bahar hai wahi use hota hai (lexical scoping).

## 9. What Happens Internally?

Regular function call hone par apna naya Execution Context banati hai jisme `this` **call-site** (kaise call hua) ke basis pe determine hota hai (Module 08 mein detail). Arrow function apna Execution Context to banati hai, lekin `this` binding **nahi karti** — `this` lookup lexically enclosing scope mein hota hai, jaise koi normal variable.

## 10. Common Mistakes

- Object methods ke liye arrow function use karna — `this` galat aata hai.
- Arrow function ko constructor ki tarah use karne ki koshish karna (`new`) — `TypeError` aata hai.
- `arguments` object expect karna arrow function ke andar — arrow functions mein `arguments` exist nahi karta (outer scope ka `arguments` use hoga, agar exist karta hai).

## 11. Edge Cases

```js
const obj = {
  name: "Test",
  regularMethod() {
    const arrowInside = () => {
      console.log(this.name); // ✅ Works! Inherits 'this' from regularMethod's context
    };
    arrowInside();
  }
};
obj.regularMethod(); // "Test"
```
Regular function ke andar nested arrow function correctly outer `this` use karta hai — ye ek bahut common aur useful pattern hai.

## 12. Real-World Usage

Arrow functions React components mein event handlers ke liye bahut common hain (class components mein `this` binding issue avoid karne ke liye), array methods (`map`, `filter`, `reduce`) ke callbacks ke liye standard hain, aur Promise `.then()` chains mein bhi common hain.

## 13. Comparison With Related Concepts

| Feature | Regular Function | Arrow Function |
|---------|---------------------|--------------------|
| Own `this` | Yes (dynamic, call-time) | No (lexical, inherited) |
| `arguments` object | Yes | No |
| Can be constructor (`new`) | Yes | No |
| Best for | Object methods, constructors | Callbacks, array methods, preserving outer `this` |

## 14. Practice Questions

1. Arrow function ka `this` kaha se aata hai?
2. Object method ke liye arrow function use karna kyun risky hai?
3. `const fn = () => { return { a: 1 }; }` aur `const fn2 = () => ({ a: 1 });` — dono mein kya same result aata hai?

## 15. Challenge

`person` object banao jisme `name` property aur ek regular function method `greetAfterDelay` ho jo `setTimeout` ke andar arrow function use karke 1 second baad `this.name` ko sahi se print kare.

## 16. Interview Questions

**Q1: Arrow functions aur regular functions mein `this` ka behavior kaise different hai?**
A: Regular functions ka `this` **dynamically** decide hota hai — call kaise hua (kis object pe, `call`/`apply`/`bind` se, ya plain call) us par depend karta hai. Arrow functions ka apna `this` binding nahi hota — wo `this` ko **lexically** (jaha define hui, surrounding scope se) inherit karte hain, aur ye kabhi change nahi hota chahe call kaise bhi ho. Isi liye arrow functions object methods ke liye risky hain but callbacks ke liye (outer `this` preserve karne ke liye) ideal hain.

**Q2: Kya arrow function ko constructor (`new` ke saath) use kar sakte hain?**
A: Nahi. Arrow functions ke paas `[[Construct]]` internal method nahi hota, jo `new` keyword ko kaam karne ke liye zaroori hota hai. `new ArrowFn()` likhne pe `TypeError: ArrowFn is not a constructor` milega. Constructors ke liye regular functions ya classes use karni chahiye.

## 17. Chapter Summary

Arrow functions concise syntax dete hain aur `this` ko lexically (surrounding scope se) inherit karte hain, unlike regular functions jinka `this` call-time pe dynamically decide hota hai. Ye callbacks/timers mein `this` preserve karne ke liye excellent hain, lekin object methods/constructors ke liye unsuitable hain.

## 18. Revision Checklist

- [ ] Arrow function syntax ki sabhi variations likh sakta hoon.
- [ ] `this` ka lexical vs dynamic behavior clear hai.
- [ ] Kab arrow function avoid karni hai (object methods, constructors), yaad hai.

---

**Next:** [03-Parameters-Arguments-Defaults-Rest.md](./03-Parameters-Arguments-Defaults-Rest.md)
