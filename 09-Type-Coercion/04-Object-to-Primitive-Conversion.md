# Object-to-Primitive Conversion

**Module:** 09-Type-Coercion
**Difficulty:** 🔴 Advanced
**Previous:** [03-Equality-Deep-Dive.md](./03-Equality-Deep-Dive.md)
**Next:** [10-Modern-JavaScript/01-ES6-Plus-Overview.md](../10-Modern-JavaScript/01-ES6-Plus-Overview.md)

---

## 1. Learning Objectives

- `ToPrimitive` operation ka exact mechanism samajhna.
- `valueOf()`, `toString()`, aur `Symbol.toPrimitive` ka use samajhna.
- Custom objects ke conversion behavior ko control karna.

## 2. Prerequisites

Module 09, chapters 01-03.

## 3. Concept in Simple Hinglish

Jab koi **object** ko primitive context mein use kiya jaata hai (jaise `+` operator mein, ya template string mein), JavaScript ko decide karna padta hai "is object ko kis primitive value se represent karu?". Ye decision `valueOf()`, `toString()`, ya custom `Symbol.toPrimitive` methods ke through hoti hai.

## 4. Technical Explanation

**`ToPrimitive(input, hint)`** ek internal algorithm hai jo 3 "hints" ke saath call ho sakta hai:
- `"number"` — jab numeric context expected hai (jaise `-`, `*`).
- `"string"` — jab string context expected hai (jaise template literals).
- `"default"` — jab hint clear nahi hai (jaise `+`, `==`).

**Order of method calls (for "default"/"number" hint):**
1. Agar object mein `Symbol.toPrimitive` method hai, use call karo — uska result use karo.
2. Nahi to, `valueOf()` try karo — agar primitive return kare, use karo.
3. Nahi to, `toString()` try karo — agar primitive return kare, use karo.
4. Kuch bhi na mile to `TypeError`.

("string" hint ke liye order reverse hota hai: `toString()` pehle try hota hai.)

## 5. Syntax

```js
const obj = {
  valueOf() { return 42; },
  toString() { return "text"; },
  [Symbol.toPrimitive](hint) { /* custom logic */ }
};
```

## 6. Basic Examples

```js
console.log([1, 2, 3] + ""); // "1,2,3" — array's toString() joins with commas
console.log({} + "");         // "[object Object]" — default Object toString()
console.log(new Date() + "");  // Some date string — Date has a custom toString()
```

## 7. Detailed Examples

**Default `Object.prototype.toString()` and `valueOf()`:**
```js
const plainObj = {};
console.log(plainObj.toString()); // "[object Object]"
console.log(plainObj.valueOf());  // {} (returns itself, which is NOT a primitive)
```
Default `valueOf()` object ko hi return karta hai (primitive nahi), isliye engine fallback karta hai `toString()` pe.

**Custom `valueOf()` — controlling numeric conversion:**
```js
const money = {
  amount: 500,
  valueOf() {
    return this.amount;
  }
};

console.log(money + 100); // 600 — valueOf() used because '+' with "default" hint tries valueOf first
console.log(money * 2);    // 1000 — valueOf() used ('number' hint also tries valueOf first)
```

**Custom `toString()` — controlling string conversion:**
```js
const user = {
  name: "Kiran",
  toString() {
    return `User: ${this.name}`;
  }
};

console.log(`${user}`);       // "User: Kiran" — template literal uses "string" hint
console.log(user + "");        // "User: Kiran" — default hint, but no valueOf() defined, so toString() used
console.log(String(user));     // "User: Kiran"
```

**Full control with `Symbol.toPrimitive` — the most explicit method:**
```js
const temperature = {
  celsius: 25,
  [Symbol.toPrimitive](hint) {
    if (hint === "number") return this.celsius;
    if (hint === "string") return `${this.celsius}°C`;
    return `Temperature: ${this.celsius}°C`; // "default" hint
  }
};

console.log(+temperature);         // 25          ("number" hint)
console.log(`${temperature}`);      // "25°C"      ("string" hint)
console.log(temperature + "");       // "Temperature: 25°C" ("default" hint)
```
`Symbol.toPrimitive` **highest priority** rakhta hai — agar define kiya gaya hai, `valueOf`/`toString` ko engine consult hi nahi karta.

**Explaining `[] + []` and `[] + {}` using this knowledge:**
```js
console.log([] + []);  // "" — both arrays convert via toString() to "" and "", concatenated = ""
console.log([] + {});   // "[object Object]" — [] → "", {} → "[object Object]", concatenated
console.log({} + []);    // "[object Object]" (in most contexts) — same logic reversed
```

## 8. Mental Model

Object-to-primitive conversion ko socho ek **translator hierarchy** — jab kisi object se "simple answer" (primitive) chahiye, engine pehle sabse specific translator (`Symbol.toPrimitive`) se poochta hai, phir "number expert" (`valueOf`), phir "general expert" (`toString`) — jo bhi valid primitive de de, wahi use hota hai.

## 9. What Happens Internally?

`ToPrimitive` ek ECMAScript-spec-defined abstract operation hai jo hint ke basis pe method-lookup order decide karta hai aur pehla valid (primitive-returning) method ka result use karta hai. Ye mechanism **every** object-in-primitive-context scenario ke peeche hai — arithmetic operators, template literals, `==` comparisons, `console.log` (indirectly), array joins.

## 10. Common Mistakes

- `valueOf()`/`toString()` ko override karna aur bhool jaana ki ye kis context mein call honge — inconsistent behavior create ho sakta hai.
- `Symbol.toPrimitive` ke bina hi custom `valueOf`/`toString` likh ke expect karna ki hamesha wahi call hoga — hint ke basis pe order change hota hai.
- Custom objects ke default `toString()` (`"[object Object]"`) ko debug karte waqt bhool jaana ki isse readable output nahi milega.

## 11. Edge Cases

```js
console.log(new Date() + 1);   // String concatenation! (Date's [Symbol.toPrimitive] defaults to "string" hint even for '+')
console.log(new Date() * 1);   // Number! (multiplication forces "number" hint)
```
`Date` object special hai — `+` operator ke saath bhi ye "default" hint use karta hai jo Date ke case mein "string" jaisa behave karta hai (Date ka apna `Symbol.toPrimitive` implementation hai jo spec mein specially defined hai).

## 12. Real-World Usage

Custom `toString()`/`valueOf()` custom classes (jaise Money, Vector, Duration classes) mein implement kiye jaate hain taaki wo naturally arithmetic/string operations mein predictably behave karein. `JSON.stringify()` bhi related but separate mechanism use karta hai (`toJSON()` method — Module 17/19 mein relevant).

## 13. Comparison With Related Concepts

| Method | Priority | Used For |
|--------|-----------|----------|
| `Symbol.toPrimitive` | Highest (if defined) | Full control over all hints |
| `valueOf()` | Used for "number"/"default" hint | Numeric-like objects |
| `toString()` | Used for "string" hint (or fallback) | String representation |

## 14. Practice Questions

1. `ToPrimitive` ke 3 possible hints kya hain?
2. `valueOf()` aur `toString()` mein se kaunsa "number" hint ke liye pehle try hota hai?
3. `Symbol.toPrimitive` define hone pe kya hota hai `valueOf`/`toString` ka?

## 15. Challenge

Ek `Vector` object banao `{x: 3, y: 4}` ke saath jisme `Symbol.toPrimitive` implement ho — `"number"` hint pe magnitude (`sqrt(x² + y²)`) return kare, `"string"` hint pe `"(3, 4)"` format return kare.

## 16. Interview Questions

**Q1: JavaScript object ko primitive value mein convert karte waqt kaunsa order follow hota hai?**
A: `ToPrimitive` operation ek "hint" (`"number"`, `"string"`, ya `"default"`) ke saath call hoti hai. Agar object mein `Symbol.toPrimitive` method defined hai, ye **sabse pehle** use hota hai aur poora control leta hai. Agar nahi hai, to "number"/"default" hint ke liye **`valueOf()`** pehle try hota hai, phir `toString()` fallback ki tarah; "string" hint ke liye order reverse hota hai — `toString()` pehle try hota hai.

**Q2: `[] + {}` `"[object Object]"` kyun deta hai?**
A: `+` operator "default" hint ke saath `ToPrimitive` call karta hai dono operands pe. Array `[]` ka default `toString()` empty string `""` return karta hai. Plain object `{}` ka default `toString()` `"[object Object]"` return karta hai (kyunki uska `valueOf()` khud object return karta hai, jo primitive nahi hai, isliye `toString()` use hota hai). Dono primitive strings ko concatenate karne se `"" + "[object Object]"` = `"[object Object]"` milta hai.

## 17. Chapter Summary

`ToPrimitive` operation objects ko primitives mein convert karta hai using hints (`number`/`string`/`default`) — pehle `Symbol.toPrimitive` (agar defined), phir `valueOf()`/`toString()` order (hint ke hisaab se). Custom classes apna conversion behavior control kar sakti hain in methods ko override karke.

## 18. Revision Checklist

- [ ] `ToPrimitive` ke 3 hints aur unka method-order yaad hai.
- [ ] `Symbol.toPrimitive` ki highest priority clear hai.
- [ ] `[] + {}` jaisi confusing expressions explain kar sakta hoon.

---

**Module 09 Complete!** Next Module: [10-Modern-JavaScript/01-ES6-Plus-Overview.md](../10-Modern-JavaScript/01-ES6-Plus-Overview.md)
