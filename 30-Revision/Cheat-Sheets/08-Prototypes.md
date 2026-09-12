# Cheat Sheet: Prototypes

**Full detail:** [10-Modern-JavaScript/03-Prototypes-Deep-Dive.md](../../10-Modern-JavaScript/03-Prototypes-Deep-Dive.md), [02-Classes-and-Inheritance.md](../../10-Modern-JavaScript/02-Classes-and-Inheritance.md)

---

## Prototype Chain
Every object has `[[Prototype]]` → linked to another object. Lookup traverses this chain until found or `null`.

```js
Object.getPrototypeOf(obj);   // Read [[Prototype]]
Object.setPrototypeOf(obj, proto); // Set it
Object.create(proto);          // Create object with given prototype
```

## Classes = Syntactic Sugar
```js
class Animal {
  constructor(name) { this.name = name; }
  speak() { return `${this.name} makes a sound`; }
}
class Dog extends Animal {
  constructor(name) { super(name); } // Must call before using 'this'
  speak() { return `${this.name} barks`; }
}
```
Internally: `Dog.prototype.__proto__ === Animal.prototype`

## Own vs Inherited
```js
obj.hasOwnProperty("key")  // Own properties only
"key" in obj                 // Own + inherited
```

## Constructor Function Pattern (⚠️ pre-ES6)
```js
function Animal(name) { this.name = name; }
Animal.prototype.speak = function() { return "sound"; };
const a = new Animal("Rex");
```

## Static vs Instance
```js
class Counter {
  static count = 0;      // On the CLASS
  instanceProp = 1;       // On each INSTANCE
}
```

## Private Fields (ES2022)
```js
class Account {
  #balance = 0; // True privacy — SyntaxError if accessed outside class
}
```

---

**Next:** [09-Promises-and-Async-Await.md](./09-Promises-and-Async-Await.md)
