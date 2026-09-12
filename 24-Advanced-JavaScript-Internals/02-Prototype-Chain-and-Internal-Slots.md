# Prototype Chain and Internal Slots

**Module:** 24-Advanced-JavaScript-Internals
**Difficulty:** 🔴 Expert
**Previous:** [01-Execution-Context-Deep-Dive.md](./01-Execution-Context-Deep-Dive.md)
**Next:** [03-Iterators-Generators-Symbols-Deep-Dive.md](./03-Iterators-Generators-Symbols-Deep-Dive.md)

---

## 1. Learning Objectives

- Internal slots (`[[...]]` notation) ka concept formally samajhna.
- `[[Prototype]]`, `[[Get]]`, `[[Set]]` internal methods ka role samajhna (Module 10, chapter 03 se aage badhate hue).
- Property lookup aur assignment ka exact algorithm samajhna.

## 2. Prerequisites

Module 10, chapter 03 (Prototypes Deep Dive) — is chapter ka formal extension.

## 3. Concept in Simple Hinglish

ECMAScript specification objects ke internal behavior ko describe karne ke liye **"internal slots"** aur **"internal methods"** ka use karti hai — ye `[[DoubleBrackets]]` notation mein likhe jaate hain aur JavaScript code se **directly accessible nahi** hote, sirf engine internally use karta hai. Inhe samajhna prototype-chain aur property-access ke exact mechanism ko reveal karta hai.

## 4. Technical Explanation

**Key Internal Slots/Methods:**
- **`[[Prototype]]`:** Object ka internal link apne prototype ki taraf (Module 10, chapter 03 mein "prototype chain" se yaad karo — accessible via `Object.getPrototypeOf()`).
- **`[[Get]](property, receiver)`:** Internal method jo property-lookup perform karta hai — prototype chain traverse karta hai.
- **`[[Set]](property, value, receiver)`:** Internal method jo property-assignment perform karta hai.
- **`[[Call]]`:** Sirf functions ke paas hota hai — enables function invocation (`fn()`).
- **`[[Construct]]`:** Sirf constructor-capable functions/classes ke paas — enables `new Fn()` (Module 05 chapter 02 mein arrow functions ke iske na hone ka mention tha).

## 5. Syntax

Internal slots/methods JavaScript syntax mein directly likhe nahi ja sakte — ye conceptual/spec-level constructs hain jo engine ke behavior ko describe karte hain.

## 6. Basic Examples

```js
function regularFn() {}
const arrowFn = () => {};

console.log(typeof regularFn.prototype); // "object" — has [[Construct]], so 'new' works
console.log(typeof arrowFn.prototype);    // "undefined" — arrow functions have NO [[Construct]]!
```

## 7. Detailed Examples

**The `[[Get]]` algorithm — the exact property-lookup mechanism:**
```js
const grandparent = { greeting: "Hello from grandparent" };
const parent = Object.create(grandparent);
const child = Object.create(parent);
child.ownProperty = "I'm on child directly";

console.log(child.greeting); // "Hello from grandparent"
```
**Formal `[[Get]]` process for `child.greeting`:**
```
1. Check child's OWN properties for "greeting" — not found.
2. Check child.[[Prototype]] (= parent)'s OWN properties for "greeting" — not found.
3. Check parent.[[Prototype]] (= grandparent)'s OWN properties for "greeting" — FOUND!
4. Return grandparent's "greeting" value.
```
Ye exact wahi process hai jo Module 10, chapter 03 mein "prototype chain lookup" kehke intuitively explain kiya tha — ab hum ise formal `[[Get]]` internal method ki tarah dekh rahe hain.

**`[[Set]]` and a subtle gotcha — "shadowing" via assignment:**
```js
const parent2 = { value: "parent's value" };
const child2 = Object.create(parent2);

console.log(child2.value); // "parent's value" (found via [[Prototype]] chain)

child2.value = "child's own value"; // [[Set]] creates a NEW OWN property on child2!

console.log(child2.value);           // "child's own value" (now found directly on child2)
console.log(parent2.value);           // "parent's value" (UNCHANGED — child2's assignment didn't affect it)
console.log(child2.hasOwnProperty("value")); // true — now it's child2's own property
```
**Key insight:** `[[Set]]` (assignment) by default creates/updates an **own property** on the object being assigned to — it does NOT walk up the prototype chain to modify an inherited property's value on the prototype itself (unless the inherited property is defined as an accessor/setter, which is a more advanced case).

**`[[Call]]` vs `[[Construct]]` — why some things can be "called" and others "constructed":**
```js
class MyClass {}
function myFunction() {}
const myArrow = () => {};

myFunction();      // ✅ Works — has [[Call]]
new myFunction();   // ✅ Works — has [[Construct]]

myArrow();          // ✅ Works — has [[Call]]
// new myArrow();    // ❌ TypeError — arrow functions have NO [[Construct]]

// MyClass();        // ❌ TypeError — classes have [[Construct]] but NOT [[Call]]!
new MyClass();      // ✅ Works
```
Ye teeno cases dikhate hain ki `[[Call]]` aur `[[Construct]]` **independent** internal methods hain — ek object dono, ek, ya koi bhi na rakh sakta hai, jo uska exact "callable"/"constructable" behavior determine karta hai.

## 8. Mental Model

Internal slots/methods ko socho ek object ke **"hidden internal organs"** ki tarah — external code (JavaScript) inhe directly nahi dekh/touch sakta (jaise tum apna dil directly touch nahi kar sakte), lekin ye organs body (object) ke fundamental behaviors (kaise "call" hota hai, kaise properties "milte" hain) control karte hain. `Object.getPrototypeOf()` jaisi public methods ek "X-ray machine" ki tarah hain — indirectly in internal structures ko observe karne deti hain.

## 9. What Happens Internally?

Ye poori chapter hi "internally kya hota hai" ke baare mein hai — engine implementations (V8, SpiderMonkey) in spec-defined internal slots/methods ko apne internal C++ data-structures ke through implement karte hain, but externally-observable behavior ECMAScript specification ke through **standardized** hai, isliye saare compliant engines consistently behave karte hain.

## 10. Common Mistakes

- Internal slots (`[[Prototype]]`) ko directly access karne ki koshish karna jaise wo normal properties hon — inhe sirf specific methods (`Object.getPrototypeOf()`, etc.) ke through hi access kiya ja sakta hai.
- `[[Set]]` ke default behavior (own-property-creation) ko "prototype ko modify karna" samajh lena.
- Arrow functions/classes ke `[[Call]]`/`[[Construct]]` availability ko yaad na rakhna, jisse unexpected `TypeError`s aate hain.

## 11. Edge Cases

`Object.create(null)` (Module 10, chapter 03 se yaad karo) ek object banata hai jiska `[[Prototype]]` **`null`** hota hai — is object ke liye `[[Get]]` algorithm turant "not found" pe terminate ho jaata hai agar property khud object pe na mile, kyunki traverse karne ke liye koi "next" prototype hi nahi hai.

## 12. Real-World Usage

Ye level ki understanding library-authors/framework-developers ke liye particularly relevant hai jo prototype-manipulation, custom-inheritance-patterns, ya meta-programming (Module 24, chapter 04 - Proxy/Reflect) implement karte hain. Application-developers ke liye, ye "why does this work this way" jaise deep debugging questions ka answer deta hai.

## 13. Comparison With Related Concepts

| Internal Slot/Method | Purpose | Related Public API |
|--------------------------|---------|--------------------------|
| `[[Prototype]]` | Links to parent object | `Object.getPrototypeOf()`/`setPrototypeOf()` |
| `[[Get]]` | Property read algorithm | Dot/bracket notation access |
| `[[Set]]` | Property write algorithm | Assignment (`=`) |
| `[[Call]]` | Enables function invocation | `fn()` |
| `[[Construct]]` | Enables `new` usage | `new Fn()` |

## 14. Practice Questions

1. `[[Get]]` algorithm property na milne pe kya karta hai (step-by-step)?
2. `child.value = x` (jab `value` prototype pe inherited hai) parent ko modify karta hai ya nahi?
3. Arrow functions ke paas kaunsa internal method missing hota hai jo unhe non-constructable banata hai?

## 15. Challenge

Module 10, chapter 03 ke "Animal"/"Dog" prototype-chain example ko wapas dekho, aur is chapter ki `[[Get]]` algorithm terminology use karke explain karo step-by-step ki `dog.speak()` call kaise resolve hoti hai jab `speak` `Animal.prototype` pe define hai, `Dog.prototype` pe nahi.

## 16. Interview Questions

**Q1: `[[Prototype]]` internal slot kya hai aur ise JavaScript code se kaise access kiya ja sakta hai?**
A: `[[Prototype]]` ek internal slot hai jo har object mein hota hai, jo us object ke "parent" (prototype) object ki taraf point karta hai — ye prototype-chain (Module 10, chapter 03) ka foundation hai. Ye internal slot **directly** JavaScript code se access nahi kiya ja sakta (jaise `obj.[[Prototype]]` valid syntax nahi hai), lekin public methods jaise `Object.getPrototypeOf(obj)` (read) aur `Object.setPrototypeOf(obj, proto)` (write) ke through indirectly access/modify kiya ja sakta hai. Historically, non-standard `__proto__` property bhi ye access deti thi, lekin modern code mein `Object.getPrototypeOf/setPrototypeOf` prefer kiya jaata hai.

**Q2: Kya ek inherited property ko assign karna (`child.value = x`, jaha `value` prototype pe hai) us property ko prototype pe modify kar deta hai?**
A: Nahi. `[[Set]]` internal method (jo assignment ko handle karta hai) by default `child` object pe ek **naya own property** create/update karta hai, chahe `value` naam ki property currently prototype-chain ke through inherited ho rahi ho. Ye "shadowing" create karta hai — `child` ab apni khud ki `value` property rakhta hai jo prototype ki `value` ko "hide" kar deti hai future lookups mein, lekin **prototype ki original property untouched rehti hai**. (Exception: agar prototype pe wo property ek **setter** ki tarah defined ho — Module 07, chapter 04 se yaad karo — tab setter trigger hota hai instead of creating an own property.)

## 17. Chapter Summary

Internal slots/methods (`[[Prototype]]`, `[[Get]]`, `[[Set]]`, `[[Call]]`, `[[Construct]]`) ECMAScript spec ke formal constructs hain jo object-behavior define karte hain, JavaScript code se directly inaccessible. `[[Get]]` prototype-chain traverse karta hai property dhundne ke liye. `[[Set]]` default-behavior mein own-property create karta hai, prototype ko modify nahi karta. `[[Call]]`/`[[Construct]]` independently decide karte hain ki koi function "callable"/"constructable" hai ya nahi.

## 18. Revision Checklist

- [ ] `[[Get]]` algorithm ka step-by-step process trace kar sakta hoon.
- [ ] `[[Set]]` ka "own-property-creation, not prototype-modification" default-behavior clear hai.
- [ ] `[[Call]]` vs `[[Construct]]` ka independent-availability concept samajh gaya.

---

**Next:** [03-Iterators-Generators-Symbols-Deep-Dive.md](./03-Iterators-Generators-Symbols-Deep-Dive.md)
