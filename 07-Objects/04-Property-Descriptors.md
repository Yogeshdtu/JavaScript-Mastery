# Property Descriptors

**Module:** 07-Objects
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [03-Object-Methods.md](./03-Object-Methods.md)
**Next:** [08-Scope-and-Closures/01-Scope-Types.md](../08-Scope-and-Closures/01-Scope-Types.md)

---

## 1. Learning Objectives

- Property descriptors ka concept samajhna — properties ke "hidden" configuration flags.
- `Object.defineProperty()` se custom property behavior banana.
- Getters aur setters ka use samajhna.

## 2. Prerequisites

Module 07, chapters 01-03.

## 3. Concept in Simple Hinglish

Har object property ke peeche kuch **hidden settings (descriptors)** hoti hain — jaise "kya ye property change ki ja sakti hai?", "kya ye `for...in` mein dikhegi?". Normally hum in settings ko directly nahi dekhte, but JavaScript humein `Object.defineProperty()` se inhe control karne deta hai — ye advanced but powerful feature hai.

## 4. Technical Explanation

Har property ka ek **descriptor object** hota hai jisme ye flags hoti hain:

- **`value`:** Property ki actual value.
- **`writable`:** Kya value change ki ja sakti hai (`=` se)?
- **`enumerable`:** Kya property `for...in`/`Object.keys()` mein dikhegi?
- **`configurable`:** Kya property delete ki ja sakti hai ya redefine ki ja sakti hai?

Normal object literal (`{key: value}`) se banayi properties ke ye sab flags **default `true`** hote hain.

## 5. Syntax

```js
Object.defineProperty(obj, "key", {
  value: someValue,
  writable: true,
  enumerable: true,
  configurable: true
});

Object.getOwnPropertyDescriptor(obj, "key");
```

## 6. Basic Examples

```js
const obj = {};
Object.defineProperty(obj, "id", {
  value: 101,
  writable: false, // Can't be changed
  enumerable: true,
  configurable: false
});

console.log(obj.id); // 101
obj.id = 999;          // Silently fails (or throws in strict mode)
console.log(obj.id);   // 101 — unchanged
```

## 7. Detailed Examples

**Checking descriptors of a normal property:**
```js
const person = { name: "Aditi" };
console.log(Object.getOwnPropertyDescriptor(person, "name"));
// { value: "Aditi", writable: true, enumerable: true, configurable: true }
```

**Non-enumerable property — hidden from iteration:**
```js
const product = { name: "Shoes", price: 2000 };

Object.defineProperty(product, "internalId", {
  value: "SKU-9081",
  enumerable: false // Won't show up in loops/Object.keys()
});

console.log(Object.keys(product));  // ["name", "price"] — internalId hidden!
console.log(product.internalId);     // "SKU-9081" — still directly accessible
```
Ye pattern useful hai jab kuch "metadata" object mein rakhni ho jo normal iteration mein interfere na kare.

**Getters and setters — computed properties:**
```js
const rectangle = {
  width: 10,
  height: 5,

  get area() {
    return this.width * this.height;
  },

  set area(value) {
    console.log(`Cannot directly set area. Adjust width/height instead.`);
  }
};

console.log(rectangle.area); // 50 — computed on the fly, called like a property, not a method!
rectangle.area = 100;         // "Cannot directly set area. Adjust width/height instead."
```
Getters/setters ek property ko **function jaisa behavior** dete hain lekin **normal property access syntax** (bina `()` ke) ke saath. Ye "computed properties" banane ke liye powerful hai.

**Real-world getter/setter — validation pattern:**
```js
const user = {
  _age: 0, // Convention: underscore prefix indicates "internal" value

  get age() {
    return this._age;
  },

  set age(value) {
    if (value < 0) {
      console.log("Age cannot be negative");
      return;
    }
    this._age = value;
  }
};

user.age = 25;
console.log(user.age); // 25

user.age = -5; // "Age cannot be negative"
console.log(user.age); // 25 — unchanged
```

## 8. Mental Model

Property descriptors ko socho ek **product ke label ke peeche ki fine print** — normal customer ko sirf price (value) dikhta hai, lekin peeche "return policy" (writable), "warranty visible on shelf?" (enumerable), "policy change ki ja sakti hai?" (configurable) jaisi hidden rules hoti hain. Getters/setters ko socho **automatic calculators** jo har baar poochne pe fresh calculation karte hain, seedha value store karne ke bajaye.

## 9. What Happens Internally?

Engine har property ko internally ek descriptor object ki tarah store karta hai (`value`/`writable`/`enumerable`/`configurable`, ya `get`/`set`/`enumerable`/`configurable` accessor properties ke liye). Jab `writable: false` set hoti hai, engine assignment attempts ko silently ignore (ya strict mode mein throw) karta hai.

## 10. Common Mistakes

- Getters ke andar `this.propertyName` (same name) use karna — infinite recursion create karta hai. Isi liye convention hai underscore-prefixed internal property (`_age`) use karna.
- `Object.defineProperty()` ko overuse karna jab simple object literal kaafi ho.
- Non-enumerable properties ko bhool jaana `JSON.stringify()` mein bhi exclude ho jaati hain.

## 11. Edge Cases

```js
const obj = { get value() { return Math.random(); } };
console.log(obj.value); // Different every time it's accessed! No caching by default.
```

## 12. Real-World Usage

Getters/setters class-based validation (Module 10) mein common hain (jaise ensuring a property never goes negative). Non-enumerable properties built-in JavaScript objects mein use hoti hain (jaise array methods `length` ke saath) taaki wo accidental iteration mein na aayen.

## 13. Comparison With Related Concepts

| Descriptor | Default (Literal) | Effect When `false` |
|------------|----------------------|--------------------------|
| `writable` | `true` | Value can't be reassigned |
| `enumerable` | `true` | Hidden from `for...in`, `Object.keys()` |
| `configurable` | `true` | Can't be deleted or redefined |

## 14. Practice Questions

1. `writable: false` set karne se kya effect hota hai?
2. `enumerable: false` property `Object.keys()` mein dikhegi ya nahi?
3. Getter ke andar same-name property access karna kyun problematic hai?

## 15. Challenge

Ek object `temperature` banao jisme `celsius` internal value ho, aur `fahrenheit` ek getter/setter ho jo automatically celsius se convert/update kare (`F = C * 9/5 + 32`).

## 16. Interview Questions

**Q1: `Object.defineProperty()` ka use case kya hai?**
A: `Object.defineProperty()` ek property ke fine-grained behavior ko control karne deta hai — jaise usse read-only banana (`writable: false`), iteration se hide karna (`enumerable: false`), ya delete/redefine hone se bachana (`configurable: false`). Ye normal object literal syntax se possible nahi hai, jaha sab flags default `true` hoti hain. Ye libraries/frameworks internals mein aur controlled APIs banane mein use hota hai.

**Q2: JavaScript mein getters aur setters kya hote hain?**
A: Getters (`get propName() {}`) aur setters (`set propName(value) {}`) special methods hain jo **property access syntax** ki tarah behave karte hain (bina `()` call kiye) lekin actually function execute karte hain. Getter kisi computed value ko return karne ke liye use hota hai (jaise `area` width*height se), setter kisi value assignment pe custom logic (jaise validation) run karne ke liye. Ye "computed properties" aur validation patterns ke liye bahut useful hain.

## 17. Chapter Summary

Har object property ke peeche descriptors (`value`/`writable`/`enumerable`/`configurable`) hote hain jo uska behavior control karte hain. `Object.defineProperty()` se ye customize kiya ja sakta hai. Getters/setters computed properties aur validation logic ko normal property-access syntax ke saath enable karte hain.

## 18. Revision Checklist

- [ ] Char descriptor flags (`value`, `writable`, `enumerable`, `configurable`) yaad hain.
- [ ] Getter/setter syntax aur use-case clear hai.
- [ ] Getter mein infinite recursion avoid karne ka pattern (`_` prefix) samajh gaya.

---

**Module 07 Complete!** Next Module: [08-Scope-and-Closures/01-Scope-Types.md](../08-Scope-and-Closures/01-Scope-Types.md)
