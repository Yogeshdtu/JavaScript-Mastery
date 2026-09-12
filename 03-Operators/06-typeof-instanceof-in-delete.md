# typeof, instanceof, in, delete

**Module:** 03-Operators
**Difficulty:** 🟡 Intermediate
**Previous:** [05-Spread-and-Rest.md](./05-Spread-and-Rest.md)
**Next:** [04-Control-Flow/01-If-Else-Switch.md](../04-Control-Flow/01-If-Else-Switch.md)

---

## 1. Learning Objectives

- `typeof` aur `instanceof` ko revise karna (Module 02 se) operator context mein.
- `in` operator se property existence check karna.
- `delete` operator se object properties remove karna.

## 2. Prerequisites

[02-Variables-and-Data-Types/06-Type-Checking.md](../02-Variables-and-Data-Types/06-Type-Checking.md)

## 3. Concept in Simple Hinglish

`in` operator check karta hai ki ek property object mein exist karti hai ya nahi (chahe value `undefined` ho). `delete` operator ek property ko object se **completely remove** kar deta hai — sirf value ko `undefined` set karna alag baat hai, `delete` property ko hi khatam kar deta hai.

## 4. Technical Explanation

**`in` operator:** `"key" in object` — checks own aur inherited properties dono.

**`delete` operator:** `delete object.key` — object ki property ko permanently remove karta hai. Arrays pe `delete` use karne se "hole" ban jaata hai (index remove nahi hota, sirf value undefined ho jaati hai) — isi liye array elements remove karne ke liye `splice()` better hai.

## 5. Syntax

```js
"key" in object
delete object.key
```

## 6. Basic Examples

```js
let user = { name: "Neha", age: 28 };

console.log("name" in user); // true
console.log("email" in user); // false

delete user.age;
console.log(user); // { name: "Neha" }
```

## 7. Detailed Examples

**`in` vs checking `undefined` — subtle difference:**
```js
let obj = { value: undefined };

console.log("value" in obj);          // true — property EXISTS, even though its value is undefined
console.log(obj.value !== undefined); // false — this check is misleading here!
```
Ye dikhata hai `in` operator zyada reliable hai property-existence check karne ke liye, `!== undefined` check se — kyunki property explicitly `undefined` value ke saath bhi exist kar sakti hai.

**`delete` on arrays — common mistake:**
```js
let fruits = ["apple", "banana", "cherry"];
delete fruits[1];

console.log(fruits);        // ["apple", <1 empty item>, "cherry"]
console.log(fruits.length); // 3 — length NOT reduced!
```
`delete` array ke index ko "hole" bana deta hai, array ki length change nahi hoti aur index bhi reserved rehta hai (empty slot). Array se element properly remove karne ke liye:
```js
let fruits2 = ["apple", "banana", "cherry"];
fruits2.splice(1, 1);
console.log(fruits2); // ["apple", "cherry"] — length properly updated to 2
```

## 8. Mental Model

`in` ko socho ek **guest list checker** jo sirf naam list mein hai ya nahi check karta hai (chahe guest abhi party mein present ho ya na ho). `delete` ko socho ek **eraser** jo poori entry hi list se mita deta hai — sirf value blank karna nahi.

## 9. What Happens Internally?

`in` operator object ki apni properties aur uski poori prototype chain traverse karta hai property key dhundne ke liye. `delete` internally object ke property descriptor table se us key ki entry ko remove karta hai — memory se object reference bhi tabhi free hota hai jab koi aur reference us value ko na rakhe (garbage collection ka concept).

## 10. Common Mistakes

- `delete` array element pe use karna array se item "remove" karne ke liye — `splice()` use karo.
- `"key" in obj` ko property ki value check karne ke liye use karna — ye sirf existence check karta hai, value nahi.
- `delete` variable pe use karne ki koshish karna — `delete` sirf object properties pe kaam karta hai, variables pe nahi (`delete myVariable` kaam nahi karega, strict mode mein error dega).

## 11. Edge Cases

```js
console.log("toString" in {}); // true — inherited from Object.prototype!
console.log({}.hasOwnProperty("toString")); // false — not an OWN property

const obj = Object.freeze({ a: 1 });
delete obj.a; // Silently fails (or throws in strict mode)
console.log(obj); // { a: 1 } — frozen objects can't be deleted from
```

`in` inherited properties bhi count karta hai — agar sirf object ki **own** properties check karni hain, `Object.hasOwnProperty()` use karo.

## 12. Real-World Usage

`delete` API responses se sensitive fields remove karne ke liye use hota hai (jaise password field client ko response bhejne se pehle remove karna). `in` operator feature-detection jaisi checks mein use hota hai (jaise `"geolocation" in navigator`).

## 13. Comparison With Related Concepts

| Operator | Purpose |
|----------|---------|
| `in` | Property existence check (own + inherited) |
| `hasOwnProperty()` | Property existence check (own only) |
| `delete` | Remove a property entirely |
| Setting to `undefined` | Property still exists, just has no value |

## 14. Practice Questions

1. `"toString" in {}` — true ya false, aur kyun?
2. `delete` array element pe use karne se kya problem hoti hai?
3. `Object.hasOwnProperty()` aur `in` operator mein kya difference hai?

## 15. Challenge

Ek object `product = { id: 1, name: "Laptop", secretCost: 500 }` banao. `delete` use karke `secretCost` ko response object se remove karo before "sending" it (console.log karke dikhao), taaki sirf `id` aur `name` bache.

## 16. Interview Questions

**Q1: `delete obj.key` aur `obj.key = undefined` mein kya difference hai?**
A: `delete obj.key` property ko object se **completely remove** kar deta hai — `"key" in obj` ab `false` return karega. `obj.key = undefined` sirf value ko `undefined` set karta hai, lekin property abhi bhi object mein exist karti hai — `"key" in obj` `true` hi rahega. Isliye agar property poori tarah remove karni hai, `delete` sahi tareeka hai.

**Q2: Arrays pe `delete` use karna kyun avoid karna chahiye?**
A: `delete` array index ki value ko remove kar deta hai, lekin index slot aur array ki `length` unchanged rehte hain — isse array mein ek "hole" (sparse array) ban jaata hai jo iteration aur array methods ke behavior ko unpredictable bana sakta hai. Element ko properly remove karne (index shift ke saath, length update ke saath) ke liye `splice()` use karna chahiye.

## 17. Chapter Summary

`in` operator property ki existence check karta hai (own + inherited), `delete` property ko object se completely remove karta hai. Arrays pe `delete` "holes" create karta hai — proper removal ke liye `splice()` use karna chahiye.

## 18. Revision Checklist

- [ ] `in` operator ka own+inherited behavior clear hai.
- [ ] `delete` vs `undefined` assignment ka difference yaad hai.
- [ ] Arrays pe `delete` avoid karne ka reason samajh gaya.

---

**Module 03 Complete!** Next Module: [04-Control-Flow/01-If-Else-Switch.md](../04-Control-Flow/01-If-Else-Switch.md)
