# Object Basics

**Module:** 07-Objects
**Difficulty:** 🟢 Beginner
**Previous:** [06-Arrays/04-Iteration-and-Advanced-Methods.md](../06-Arrays/04-Iteration-and-Advanced-Methods.md)
**Next:** [02-Destructuring-and-Spread.md](./02-Destructuring-and-Spread.md)

---

## 1. Learning Objectives

- Object create karna, properties access/modify/delete karna.
- Dot notation vs bracket notation samajhna.
- Dynamic aur computed property names use karna.
- Object methods likhna (shorthand syntax).

## 2. Prerequisites

Module 06 complete.

## 3. Concept in Simple Hinglish

Object ek **key-value pairs** ka collection hai — jaise ek real-world "profile card" jisme naam, age, city jaisi labeled information hoti hai. Arrays ordered lists ke liye hain, objects **named/structured data** ke liye hain.

## 4. Technical Explanation

Object literal syntax `{}` se banta hai. Har property ek **key** (string ya symbol) aur uski **value** (kisi bhi type ki) hoti hai. Properties ko access karne ke 2 tareeke: **dot notation** (`obj.key`) aur **bracket notation** (`obj["key"]`).

## 5. Syntax

```js
let obj = {
  key1: value1,
  key2: value2,
  method1() { /* ... */ }
};

obj.key1;
obj["key1"];
obj.newKey = value;
delete obj.key1;
```

## 6. Basic Examples

```js
let person = {
  name: "Kavya",
  age: 28,
  isEmployed: true
};

console.log(person.name);      // "Kavya"
console.log(person["age"]);    // 28

person.city = "Bangalore";
console.log(person);
```

## 7. Detailed Examples

**Dot vs Bracket notation — when bracket is required:**
```js
let user = { "first-name": "Rahul", age: 25 };

// console.log(user.first-name); // ❌ Invalid — hyphen breaks dot notation!
console.log(user["first-name"]); // ✅ "Rahul" — bracket notation required for invalid identifiers

let key = "age";
console.log(user[key]); // ✅ 25 — bracket notation allows DYNAMIC keys
// console.log(user.key); // ❌ undefined — dot notation treats 'key' literally, not as a variable
```
Bracket notation **zaroori** hai jab: (1) key mein special characters/spaces hain, (2) key **dynamically** (variable se) aani hai.

**Computed property names — building keys dynamically:**
```js
function createSetting(settingName, value) {
  return {
    [settingName]: value // Computed property name
  };
}

console.log(createSetting("theme", "dark")); // { theme: "dark" }
console.log(createSetting("volume", 80));      // { volume: 80 }
```

**Method shorthand — ES6 concise syntax:**
```js
// ⚠️ Older way
let obj1 = {
  greet: function () {
    return "Hello";
  }
};

// ✅ Modern shorthand
let obj2 = {
  greet() {
    return "Hello";
  }
};
```

**Property value shorthand:**
```js
let name = "Meera";
let age = 24;

// ⚠️ Older way
let person1 = { name: name, age: age };

// ✅ Modern shorthand — when key and variable name match
let person2 = { name, age };
console.log(person2); // { name: "Meera", age: 24 }
```

## 8. Mental Model

Object ko socho ek **filing cabinet** — har drawer pe ek label (key) hai, andar ki file (value) us drawer se associated hai. Dot notation seedha label bol ke drawer kholna hai. Bracket notation ek "chit" (variable) ke through drawer dhundna hai — jab label khud pata nahi hota, sirf variable mein store hai.

## 9. What Happens Internally?

Engine object ko ek **hash table jaisi structure** mein store karta hai (modern engines mein optimization ke liye "hidden classes"/"shapes" bhi use hoti hain — Module 24 mein detail). Property access `O(1)` (roughly constant time) hoti hai average case mein, chahe object mein kitni bhi properties hon.

## 10. Common Mistakes

- Dynamic keys ke liye dot notation use karne ki koshish karna (`obj.variableKey` kaam nahi karega jaisa expect karte ho).
- Property shorthand ko galat samajhna — `{ name }` sirf tab kaam karta hai jab variable `name` already exist karti ho.
- Object ki properties ko `for...in` se iterate karte time inherited properties ka dhyaan na rakhna (Module 04, chapter 03 se yaad karo).

## 11. Edge Cases

```js
let obj = {};
obj[true] = "yes";
obj[123] = "number key";
console.log(obj); // { "123": "number key", "true": "yes" } — keys are always converted to strings (except Symbols)!
```
Object keys (except Symbols) **always strings** hote hain internally — chahe tum number ya boolean ki tarah likho, engine unhe string mein convert kar deta hai.

## 12. Real-World Usage

Objects API responses (JSON data), configuration settings, form data, aur application state represent karne ke liye sabse common data structure hain JavaScript mein.

## 13. Comparison With Related Concepts

| Aspect | Dot Notation | Bracket Notation |
|--------|----------------|----------------------|
| Syntax | `obj.key` | `obj["key"]` |
| Dynamic keys | ❌ No | ✅ Yes |
| Special characters in key | ❌ No | ✅ Yes |
| Readability | Better | Slightly more verbose |

## 14. Practice Questions

1. Bracket notation kab zaroori ho jaati hai dot notation ke bajaye?
2. Computed property name syntax likho ek example ke saath.
3. Object keys internally kis type mein convert ho jaati hain (Symbols ko chhodkar)?

## 15. Challenge

Ek function `buildUser(field, value)` likho jo computed property names use karke ek object return kare `{ [field]: value }`. Isse teen baar call karke ek complete user object banao (`name`, `email`, `age` fields ke saath), phir `Object.assign()` ya spread se unhe merge karo.

## 16. Interview Questions

**Q1: Object properties access karne ke liye dot notation aur bracket notation mein kya difference hai?**
A: Dot notation (`obj.key`) simple aur readable hai, lekin key ka naam **literally** code mein likha hona chahiye aur valid identifier hona chahiye (no spaces, no special characters, no starting with digit). Bracket notation (`obj["key"]` ya `obj[variable]`) flexible hai — ye **dynamic keys** (variables se) support karta hai aur special characters wali keys ke liye zaroori hai. Jab key runtime pe decide honi ho (jaise loop mein), bracket notation must-use hai.

**Q2: JavaScript object keys ka type kya hota hai?**
A: Object keys (property names) hamesha **strings** hoti hain, ya **Symbols** (special unique identifiers). Agar tum koi bhi dusra type (number, boolean) key ki tarah use karte ho, JavaScript automatically usse string mein convert kar deta hai internally. Isliye `obj[1]` aur `obj["1"]` actually same property ko refer karte hain.

## 17. Chapter Summary

Objects key-value pairs ka collection hain, structured/named data ke liye best. Dot notation simple static keys ke liye hai, bracket notation dynamic/special keys ke liye zaroori hai. Object keys internally strings (ya Symbols) mein convert ho jaati hain. ES6 shorthand syntax (method shorthand, property shorthand, computed keys) code ko concise banata hai.

## 18. Revision Checklist

- [ ] Dot vs bracket notation ka use-case clear hai.
- [ ] Computed property names likh sakta hoon.
- [ ] Object keys strings/symbols hoti hain, ye yaad hai.

---

**Next:** [02-Destructuring-and-Spread.md](./02-Destructuring-and-Spread.md)
