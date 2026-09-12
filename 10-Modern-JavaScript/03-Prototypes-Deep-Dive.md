# Prototypes Deep Dive

**Module:** 10-Modern-JavaScript
**Difficulty:** 🔴 Advanced
**Previous:** [02-Classes-and-Inheritance.md](./02-Classes-and-Inheritance.md)
**Next:** [04-Map-Set-WeakMap-WeakSet.md](./04-Map-Set-WeakMap-WeakSet.md)

---

## 1. Learning Objectives

- `[[Prototype]]` aur prototype chain ka exact mechanism samajhna.
- Constructor functions se pre-ES6 inheritance samajhna.
- `Object.create()` ka use samajhna.
- Classes ke "underlying" prototype behavior ko connect karna.

## 2. Prerequisites

[02-Classes-and-Inheritance.md](./02-Classes-and-Inheritance.md)

## 3. Concept in Simple Hinglish

**Prototype** ek "backup object" hai jise JavaScript check karta hai jab tumhe kisi object mein directly koi property/method nahi milti. Ye ek chain (silsila) banata hai — object A prototype B ko check karta hai, B apne prototype C ko check karta hai, aur ye chalta hai jab tak property mil jaaye ya chain khatam ho jaaye. Classes ye sab "peeche se" prototypes hi use karti hain.

## 4. Technical Explanation

Har JavaScript object ka ek internal `[[Prototype]]` link hota hai (accessible via `Object.getPrototypeOf(obj)` ya legacy `__proto__`) jo dusre object ki taraf point karta hai. Jab property access hoti hai:
1. Object mein khud (own property) check hota hai.
2. Nahi milne pe, `[[Prototype]]` mein check hota hai.
3. Ye chain continue hoti hai jab tak property mile ya `[[Prototype]]` `null` ho jaaye (chain ka end — `Object.prototype` ke baad).

**Constructor Functions (⚠️ pre-ES6 way):**
```js
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function () {
  return `Hi, I'm ${this.name}`;
};
```

## 5. Syntax

```js
Object.getPrototypeOf(obj);
Object.setPrototypeOf(obj, proto);
Object.create(protoObject);
ConstructorFn.prototype.method = function() {};
```

## 6. Basic Examples

```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function () {
  return `${this.name} makes a sound`;
};

const cat = new Animal("Whiskers");
console.log(cat.speak()); // "Whiskers makes a sound"
console.log(cat.hasOwnProperty("speak")); // false — 'speak' is on the PROTOTYPE, not the object itself
console.log(Animal.prototype.hasOwnProperty("speak")); // true
```

## 7. Detailed Examples

**Visualizing the prototype chain:**
```js
const cat2 = new Animal("Tom");

console.log(Object.getPrototypeOf(cat2) === Animal.prototype); // true
console.log(Object.getPrototypeOf(Animal.prototype) === Object.prototype); // true
console.log(Object.getPrototypeOf(Object.prototype)); // null — end of the chain
```
**Chain visualization:** `cat2` → `Animal.prototype` → `Object.prototype` → `null`

**Pre-ES6 inheritance using constructor functions (⚠️ Legacy — classes now do this cleanly):**
```js
function Animal2(name) {
  this.name = name;
}
Animal2.prototype.speak = function () {
  return `${this.name} makes a sound`;
};

function Dog2(name, breed) {
  Animal2.call(this, name); // Manually calling "parent constructor" (like super())
  this.breed = breed;
}
Dog2.prototype = Object.create(Animal2.prototype); // Setting up the prototype chain manually
Dog2.prototype.constructor = Dog2; // Fixing the constructor reference

Dog2.prototype.bark = function () {
  return `${this.name} barks!`;
};

const rex = new Dog2("Rex", "Labrador");
console.log(rex.speak()); // "Rex makes a sound" — inherited via prototype chain
console.log(rex.bark());   // "Rex barks!"
```
**✅ Modern equivalent:** Yahi ye hai jo `class Dog extends Animal` **internally** karta hai — `extends`/`super` sirf isi complex manual process ke liye syntactic sugar hai. Isi liye samajhna zaroori hai ki classes "magic" nahi hain, balki prototype mechanism ko simplify karti hain.

**`Object.create()` — direct prototype-based object creation:**
```js
const vehiclePrototype = {
  start() {
    return `${this.name} is starting`;
  }
};

const car = Object.create(vehiclePrototype);
car.name = "Sedan";
console.log(car.start()); // "Sedan is starting" — 'start' found via prototype chain
```

**Own properties vs inherited properties:**
```js
console.log(car.hasOwnProperty("name"));  // true — own property
console.log(car.hasOwnProperty("start")); // false — inherited via prototype
console.log("start" in car);                // true — 'in' checks the whole chain
```

## 8. Mental Model

Prototype chain ko socho ek **"ask your parents" system** — jab tumhare paas khud ki koi cheez (property) nahi hoti, tum apne parent (prototype) se poochte ho. Agar parent ke paas bhi nahi hai, wo apne parent (grandparent prototype) se poochta hai. Ye chain chalti rehti hai jab tak koi jawab mile ya "family tree" khatam ho jaaye (`null`).

## 9. What Happens Internally?

Engine har object ke internal `[[Prototype]]` slot ko maintain karta hai. Property lookup ek loop hai jo `[[Prototype]]` links ko follow karta hai jab tak property milti hai ya `null` aata hai. Modern engines (V8) is process ko "hidden classes"/"shapes" ke through heavily optimize karte hain, taaki repeated lookups fast rahen.

## 10. Common Mistakes

- Prototype methods ko object ki "own" property samajhna — `hasOwnProperty()` se check karo.
- `Object.create(null)` ka effect na samajhna — ye ek object banata hai **bina kisi prototype ke** (even `Object.prototype` bhi nahi milta) — koi inherited methods (`toString`, etc.) available nahi honge.
- Prototype ko directly modify karna in a way that affects ALL instances unintentionally (jaise `Array.prototype.myMethod = ...` — global pollution).

## 11. Edge Cases

```js
console.log(Object.create(null)); // A truly "bare" object with no inherited methods at all
const bare = Object.create(null);
// bare.toString(); // ❌ TypeError — toString is not a function, because there's no prototype chain!
```

## 12. Real-World Usage

Prototype-based inheritance JavaScript ka original OOP mechanism hai — classes ise abstract karti hain, lekin library internals (jaise older jQuery-style code) aur performance-critical code abhi bhi direct prototype manipulation use karte hain. Samajhna zaroori hai debugging aur advanced interview questions ke liye.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** `class`/`extends`/`super` syntax — same underlying mechanism, cleaner syntax.
**⚠️ Legacy:** Constructor functions + manual `prototype` chain setup — purane codebases mein common, samajhna zaroori.

## 14. Practice Questions

1. Prototype chain ka "end" kya hota hai?
2. `hasOwnProperty()` aur `in` operator mein prototype-chain-related difference kya hai?
3. `Object.create(null)` se banaya object normal object se kaise different hai?

## 15. Challenge

Constructor function approach use karke (class syntax nahi) ek `Shape` "class" banao jisme `area()` method prototype pe ho, aur `Circle` "class" banao jo manually `Object.create()` se inherit kare aur `area()` override kare.

## 16. Interview Questions

**Q1: Prototype chain kya hai aur property lookup kaise kaam karta hai?**
A: Har object ka ek internal `[[Prototype]]` link hota hai kisi dusre object ki taraf. Jab property access hoti hai, engine pehle object ki apni (own) properties check karta hai; nahi milne pe, `[[Prototype]]` ki taraf jaata hai, aur ye process repeat hota hai jab tak property mile ya chain `null` pe end ho jaaye (ye `Object.prototype` ke baad hota hai). Ye mechanism inheritance ko enable karta hai bina properties ko physically copy kiye.

**Q2: JavaScript classes internally kaise kaam karti hain — kya wo "real" classes hain jaisi Java/C++ mein hoti hain?**
A: JavaScript classes **syntactic sugar** hain prototype-based inheritance ke upar — inka koi separate "class" mechanism engine level pe nahi hai jo Java/C++ jaisi languages mein hota hai. Jab `class Dog extends Animal` likha jaata hai, engine internally `Dog.prototype` ko `Animal.prototype` se link kar deta hai (prototype chain), aur `super()` parent constructor ko call karta hai. Ye bilkul wahi mechanism hai jo pre-ES6 mein manually `Object.create()` aur `.call()` se implement kiya jaata tha.

## 17. Chapter Summary

Prototype chain JavaScript ka fundamental inheritance mechanism hai — objects apni "own" properties na milne pe apne `[[Prototype]]` (aur uske aage) mein search karte hain. Constructor functions + manual prototype setup (⚠️ legacy) is mechanism ko explicitly istemaal karte the; classes (✅ modern) same mechanism ko clean syntax deti hain.

## 18. Revision Checklist

- [ ] Prototype chain lookup process trace kar sakta hoon.
- [ ] Constructor-function-based inheritance ka pattern samajh gaya.
- [ ] Classes "prototype ke upar syntactic sugar" hain, ye clear hai.

---

**Next:** [04-Map-Set-WeakMap-WeakSet.md](./04-Map-Set-WeakMap-WeakSet.md)
