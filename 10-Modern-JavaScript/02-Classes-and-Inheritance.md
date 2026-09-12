# Classes and Inheritance

**Module:** 10-Modern-JavaScript
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [01-ES6-Plus-Overview.md](./01-ES6-Plus-Overview.md)
**Next:** [03-Prototypes-Deep-Dive.md](./03-Prototypes-Deep-Dive.md)

---

## 1. Learning Objectives

- Class syntax se objects create karna.
- `extends` aur `super` se inheritance implement karna.
- Class ke `this` behavior aur strict-mode-by-default nature samajhna.

## 2. Prerequisites

Modules 01-09, especially [08-Scope-and-Closures/05-this-Keyword.md](../08-Scope-and-Closures/05-this-Keyword.md).

## 3. Concept in Simple Hinglish

Class ek **blueprint/template** hai jisse hum multiple similar objects bana sakte hain — jaise ek "Car" class se multiple actual cars (objects) bante hain, sab ka structure same but data different. `extends` ek class ko dusri class ka "child" banata hai — inheritance ke through parent ki properties/methods reuse karne deta hai.

## 4. Technical Explanation

Classes JavaScript mein **syntactic sugar** hain prototype-based inheritance ke upar (next chapter mein prototypes deeply cover honge). Class ke andar:
- **`constructor()`:** Object create hone pe automatically call hota hai, initial properties set karta hai.
- **Methods:** Class body mein directly define hote hain (automatically non-enumerable, prototype pe store hote hain).
- **`extends`:** Ek class ko dusri class se inherit karata hai.
- **`super()`:** Parent class ke constructor/methods ko call karta hai.

**Important:** Classes automatically **strict mode** mein hoti hain (Module 01, chapter 05 se yaad karo).

## 5. Syntax

```js
class ClassName {
  constructor(param) {
    this.property = param;
  }
  method() { /* ... */ }
}

class ChildClass extends ParentClass {
  constructor(param) {
    super(param); // Must call before using 'this'
  }
}
```

## 6. Basic Examples

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  greet() {
    return `Hi, I'm ${this.name}`;
  }
}

const p1 = new Person("Ira", 24);
console.log(p1.greet()); // "Hi, I'm Ira"
```

## 7. Detailed Examples

**Inheritance with `extends` and `super()`:**
```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} makes a sound.`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Calls Animal's constructor — MUST be called before 'this'
    this.breed = breed;
  }
  speak() { // Overriding the parent method
    return `${this.name} (${this.breed}) barks!`;
  }
  parentSpeak() {
    return super.speak(); // Explicitly calling the parent's version
  }
}

const dog = new Dog("Rex", "Labrador");
console.log(dog.speak());       // "Rex (Labrador) barks!" — overridden version
console.log(dog.parentSpeak()); // "Rex makes a sound." — parent's original version
console.log(dog instanceof Animal); // true — inheritance chain
console.log(dog instanceof Dog);     // true
```

**Static methods — belong to the class itself, not instances:**
```js
class MathHelper {
  static square(n) {
    return n * n;
  }
}

console.log(MathHelper.square(5)); // 25
// const helper = new MathHelper();
// helper.square(5); // ❌ TypeError — static methods aren't on instances
```

**Getters/setters in classes (Module 07, chapter 04 revisited):**
```js
class Circle {
  constructor(radius) {
    this.radius = radius;
  }
  get area() {
    return Math.PI * this.radius ** 2;
  }
}

const c = new Circle(5);
console.log(c.area.toFixed(2)); // "78.54" — accessed like a property, computed on the fly
```

**⚠️ Common error — calling a class without `new`:**
```js
class Foo {}
// Foo(); // ❌ TypeError: Class constructor Foo cannot be invoked without 'new'
```
Classes **must** be called with `new` — unlike regular functions jo bina `new` ke bhi call ho sakte hain (jaha `this` different behave karta).

**⚠️ Common error — forgetting `super()` in a child constructor:**
```js
class Base {
  constructor() { this.x = 1; }
}
class Derived extends Base {
  constructor() {
    // this.y = 2; // ❌ ReferenceError: Must call super constructor before accessing 'this'
    super();
    this.y = 2; // ✅ Now safe
  }
}
```

## 8. Mental Model

Class ko socho ek **cookie-cutter mold** — mold khud koi cookie nahi hai, lekin isse jitni cookies (instances) chahiye bana sakte ho, sab same shape (structure) ke saath but individually customizable (different `name`, `age` values). `extends`/`super` ko socho ek "**improved mold**" jo purane mold ka base design use karta hai but apne extra features add karta hai.

## 9. What Happens Internally?

Class syntax "under the hood" prototype-based inheritance hi create karta hai (agla chapter mein detail) — class methods `ClassName.prototype` pe store hoti hain, `extends` `Child.prototype.__proto__` ko `Parent.prototype` se link karta hai. `super()` parent constructor ko call karta hai aur `this` ko properly initialize karta hai before child constructor use kar sake.

## 10. Common Mistakes

- Child class constructor mein `super()` call karna bhool jaana (ya `this` use karne se pehle call na karna).
- Class ko bina `new` ke call karne ki koshish karna.
- Static methods ko instance pe call karne ki koshish karna.

## 11. Edge Cases

```js
class Example {
  #privateField = 10; // Preview — full detail in chapter 07

  showPrivate() {
    return this.#privateField;
  }
}
const ex = new Example();
console.log(ex.showPrivate()); // 10
// console.log(ex.#privateField); // ❌ SyntaxError — truly private
```

## 12. Real-World Usage

Classes React class components (legacy pattern), Node.js backend models/services, aur object-oriented design patterns (Module 25) mein use hote hain. Modern React prefer karta hai function components + hooks, lekin classes samajhna abhi bhi zaroori hai bahut sare existing codebases aur backend frameworks ke liye.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** Class syntax jab OOP-style design zaroori ho.
**⚠️ Legacy but important to know:** Constructor functions + `Object.create()` (next chapter) — classes ke pehle inheritance implement karne ka tareeka tha.

## 14. Practice Questions

1. Child class constructor mein `super()` kab call karna zaroori hai?
2. Static method aur instance method mein kya difference hai?
3. Class ko bina `new` ke call karne pe kya hota hai?

## 15. Challenge

Ek `Shape` base class banao jisme `area()` method ho (return `0`). `Circle` aur `Rectangle` classes banao jo `Shape` se extend hon aur `area()` ko override karke sahi calculation dein.

## 16. Interview Questions

**Q1: `extends` aur `super` ka use kya hai?**
A: `extends` ek class ko dusri class (parent) se inherit karne deta hai, taaki child class parent ki properties aur methods reuse kar sake bina unhe dobara likhe. `super()` parent class ke constructor ko call karta hai (child constructor mein `this` use karne se pehle zaroori hai), aur `super.methodName()` parent ke kisi specific method ko call karne ke liye use hota hai — especially jab child ne wahi method override kiya ho lekin parent ka original version bhi chahiye ho.

**Q2: JavaScript classes hoisted hoti hain ya nahi?**
A: Classes technically hoist hoti hain (jaise `let`/`const`) lekin wo **Temporal Dead Zone** mein rehti hain — matlab class declaration se pehle use karna `ReferenceError` dega, function declarations ki tarah nahi jo fully usable hain hoisting ke baad. Isi liye classes ko use karne se pehle define karna zaroori hai, code order mein.

## 17. Chapter Summary

Classes objects banane ka blueprint-based (syntactic sugar over prototypes) tareeka hain. `constructor()` initial setup karta hai, `extends`/`super` inheritance enable karte hain. Classes automatically strict mode mein hain, bina `new` ke call nahi ho sakti, aur child constructors mein `super()` call karna zaroori hai `this` use karne se pehle.

## 18. Revision Checklist

- [ ] `constructor`, `extends`, `super` ka syntax aur use-case clear hai.
- [ ] Static vs instance methods ka difference yaad hai.
- [ ] Classes strict-mode-by-default aur `new`-required behavior samajh gaya.

---

**Next:** [03-Prototypes-Deep-Dive.md](./03-Prototypes-Deep-Dive.md)
