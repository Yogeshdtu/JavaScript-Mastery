# call, apply, and bind

**Module:** 08-Scope-and-Closures
**Difficulty:** 🟠 Advanced
**Previous:** [05-this-Keyword.md](./05-this-Keyword.md)
**Next:** [09-Type-Coercion/01-Truthy-and-Falsy.md](../09-Type-Coercion/01-Truthy-and-Falsy.md)

---

## 1. Learning Objectives

- `call()`, `apply()`, `bind()` se explicitly `this` set karna seekhna.
- Teeno methods ke syntax differences samajhna.
- Real-world use-cases (borrowing methods, partial application) dekhna.

## 2. Prerequisites

[05-this-Keyword.md](./05-this-Keyword.md)

## 3. Concept in Simple Hinglish

`call()`, `apply()`, aur `bind()` teeno methods humein manually decide karne dete hain ki function call hote waqt `this` ki value **kya honi chahiye** — bina function ko kisi object ka actual method banaye. Ye "explicit binding" hai (previous chapter mein mentioned rule #2).

## 4. Technical Explanation

| Method | Calls Immediately? | Arguments Format | Returns |
|--------|------------------------|----------------------|---------|
| `call(thisArg, arg1, arg2, ...)` | ✅ Yes | Comma-separated | Function's return value |
| `apply(thisArg, [argsArray])` | ✅ Yes | Array | Function's return value |
| `bind(thisArg, arg1, ...)` | ❌ No | Comma-separated | A NEW function (not called yet) |

## 5. Syntax

```js
fn.call(thisValue, arg1, arg2);
fn.apply(thisValue, [arg1, arg2]);
const boundFn = fn.bind(thisValue, arg1);
```

## 6. Basic Examples

```js
function introduce(greeting) {
  console.log(`${greeting}, I'm ${this.name}`);
}

const person = { name: "Kabir" };

introduce.call(person, "Hi");   // "Hi, I'm Kabir"
introduce.apply(person, ["Hey"]); // "Hey, I'm Kabir"

const boundIntroduce = introduce.bind(person, "Yo");
boundIntroduce(); // "Yo, I'm Kabir" — called later!
```

## 7. Detailed Examples

**`call()` vs `apply()` — the only difference is argument format:**
```js
function sum(a, b, c) {
  return a + b + c;
}

console.log(sum.call(null, 1, 2, 3));   // 6 — arguments passed individually
console.log(sum.apply(null, [1, 2, 3])); // 6 — arguments passed as an array
```
Yaad rakhne ka tareeka: "**A**pply takes an **A**rray."

**Method borrowing — a classic real use-case:**
```js
const car = { name: "Sedan", displayInfo() { return `This is a ${this.name}`; } };
const bike = { name: "Sports Bike" };

console.log(car.displayInfo.call(bike)); // "This is a Sports Bike"
```
`bike` ka apna `displayInfo` method nahi hai, lekin `call()` use karke humne `car` ka method **"borrow"** kar liya aur `bike` ke context mein chalaya. Ye pattern kabhi-kabhi array-like objects ke saath array methods use karne ke liye bhi hota hai:
```js
function sumArguments() {
  return Array.prototype.slice.call(arguments).reduce((a, b) => a + b, 0);
  // Modern alternative: [...arguments].reduce(...) or use rest parameters
}
console.log(sumArguments(1, 2, 3)); // 6
```

**`bind()` — creating a permanently-attached function:**
```js
const user = {
  name: "Meher",
  greet() {
    console.log(`Hello, ${this.name}`);
  }
};

const detachedGreet = user.greet; // Losing 'this' (Module 08, chapter 05 problem)
// detachedGreet(); // "Hello, undefined"

const boundGreet = user.greet.bind(user); // Permanently bound to 'user'
detachedGreet; // still broken
boundGreet();   // "Hello, Meher" ✅ — works no matter how it's called later!

setTimeout(boundGreet, 100); // "Hello, Meher" — even in a totally different context
```
`bind()` ka result **permanently** us `this` value se attached rehta hai, chahe use kahi se bhi baad mein call karo — ye `setTimeout`/event-handlers mein `this` preserve karne ka ek classic (pre-arrow-function) solution hai.

**Partial application with `bind()`:**
```js
function multiply(a, b) {
  return a * b;
}

const double = multiply.bind(null, 2); // Pre-filling the first argument
console.log(double(5)); // 10 (2 * 5)
console.log(double(10)); // 20 (2 * 10)
```
`bind()` extra arguments bhi "lock" kar sakta hai — ye "partial application" ka basic form hai.

## 8. Mental Model

`call()`/`apply()` ko socho **"turant kisi ke naam pe kaam karo"** — immediately execute hota hai given identity ke saath. `bind()` ko socho **"permanently kisi ke naam pe kaam karne ke liye ek dedicated agent bana do"** — naya function milta hai jo hamesha wahi identity use karega, jab bhi call ho.

## 9. What Happens Internally?

Ye teeno methods `Function.prototype` pe defined hain — har function inhe inherit karta hai (Module 10 mein prototypes detail). Call/apply function ko turant invoke karte hain specified `this` binding ke saath. `bind()` ek **naya wrapper function** return karta hai jiske andar original function aur specified `this`/arguments closure ke through "captured" hote hain (Module 08, chapter 04 se connect karo) — jab wrapper call hota hai, wo internally original function ko correct `this` ke saath call karta hai.

## 10. Common Mistakes

- `call`/`apply`/`bind` mein `this` value bhool jaana ya `undefined`/`null` pass karna jab actual object chahiye tha (non-strict mode mein `null`/`undefined` global object mein convert ho jaate hain).
- `call()` aur `apply()` ke arguments format mein confuse ho jaana (individual vs array).
- `bind()` ka result store na karna aur wonder karna ki `this` fix kyun nahi hua — `bind()` original function ko modify nahi karta, naya function return karta hai.

## 11. Edge Cases

```js
function showThis() { console.log(this); }
const bound1 = showThis.bind({ a: 1 });
const bound2 = bound1.bind({ a: 2 }); // ⚠️ Re-binding has NO effect!
bound2(); // { a: 1 } — the FIRST bind() wins, subsequent binds are ignored
```
Ek baar `bind()` ho jaane ke baad, `this` ko dobara `bind()` se change nahi kiya ja sakta — pehla binding "permanent" hai.

## 12. Real-World Usage

`bind()` React class components mein event handlers ke liye historically use hota tha (`this.handleClick = this.handleClick.bind(this)` constructor mein). `call`/`apply` method-borrowing aur function-utility libraries mein use hote hain. Modern code arrow functions ko often prefer karta hai jaha possible ho, lekin `call`/`apply`/`bind` samajhna abhi bhi zaroori hai legacy code aur library internals ke liye.

## 13. Comparison With Related Concepts

| Approach | When to Use |
|----------|--------------|
| Arrow functions | Preserving `this` in callbacks (modern, most common) |
| `bind()` | Creating a permanently `this`-attached function (event handlers, partial application) |
| `call()`/`apply()` | One-time explicit `this` control, method borrowing |

## 14. Practice Questions

1. `call()` aur `apply()` mein syntax ka difference kya hai?
2. `bind()` turant function call karta hai ya naya function return karta hai?
3. Ek baar bind hone ke baad, kya `this` ko dobara bind se change kiya ja sakta hai?

## 15. Challenge

Ek function `logMessage(prefix, message)` likho jiska `this.name` use ho prefix ke saath. `bind()` use karke isko 2 different objects (`{name: "LoggerA"}`, `{name: "LoggerB"}`) ke saath permanently attach karo aur test karo.

## 16. Interview Questions

**Q1: `call()`, `apply()`, aur `bind()` mein kya difference hai?**
A: Teeno explicit `this` binding ke liye hain. `call(thisArg, arg1, arg2)` aur `apply(thisArg, [args])` function ko **turant call** kar dete hain — difference sirf arguments pass karne ke format mein hai (individual vs array). `bind(thisArg, ...args)` function ko turant call **nahi** karta — ye ek **naya function return karta hai** jiska `this` (aur optionally kuch initial arguments) permanently set ho gaye hain, jise jab bhi chaho baad mein call kar sakte ho.

**Q2: `bind()` ka ek practical real-world use-case batao.**
A: `bind()` ka classic use-case event handlers mein `this` preserve karna hai — jaise React class components mein `this.handleClick = this.handleClick.bind(this)` constructor mein likhna, taaki jab `handleClick` button click pe call ho (jaha normally `this` lost ho jaata, Module 08 chapter 05 dekha tha), tab bhi wo sahi component instance ko `this` ki tarah use kare. Dusra use-case hai "partial application" — kisi function ke pehle kuch arguments ko permanently "lock" karna (jaise `multiply.bind(null, 2)` se ek `double` function banana).

## 17. Chapter Summary

`call()` aur `apply()` function ko turant call karte hain explicit `this` ke saath (arguments format mein difference — individual vs array). `bind()` naya function return karta hai jiska `this` permanently fix ho jaata hai, baad mein call karne ke liye. Ye teeno methods `this`-related bugs solve karne ka pre-arrow-function classic approach hain, aur legacy code samajhne ke liye zaroori hain.

## 18. Revision Checklist

- [ ] `call` vs `apply` ka argument-format difference yaad hai.
- [ ] `bind()` naya function return karta hai, turant call nahi karta — clear hai.
- [ ] Method borrowing aur partial application ke examples samajh gaye.

---

**Module 08 Complete!** Next Module: [09-Type-Coercion/01-Truthy-and-Falsy.md](../09-Type-Coercion/01-Truthy-and-Falsy.md)
