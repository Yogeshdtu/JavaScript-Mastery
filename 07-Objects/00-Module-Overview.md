# Module 07 — Objects

**Difficulty:** 🟡 Intermediate
**Chapters:** 4
**Estimated time:** 5-7 hours
**Prerequisites:** [Module 06](../06-Arrays/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Agar arrays "lists" hain, to objects **"cheezein"** hain — ek user, ek product, ek settings config. Har cheez ke apne named properties hote hain (`name`, `price`, `isActive`).

JavaScript mein objects **har jagah** hain — arrays bhi internally objects hain, functions bhi objects hain, API se aane wala data object hota hai. Isliye ye module core hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Object Basics](./01-Object-Basics.md) | Banane, dot vs bracket notation, nesting, methods |
| 02 | [Destructuring and Spread](./02-Destructuring-and-Spread.md) | ✅ `{ a, b } = obj`, renaming, defaults, `...spread` |
| 03 | [Object Methods](./03-Object-Methods.md) | `Object.keys/values/entries`, `assign`, `freeze` |
| 04 | [Property Descriptors](./04-Property-Descriptors.md) | Getters/setters, `writable`, `enumerable` |

---

## Is Module Ke Baad Tum

- [ ] Nested objects se data nikaal paoge (API responses handle karne ke liye zaroori).
- [ ] Destructuring likh paoge renaming aur default values ke saath.
- [ ] Objects ko safely copy aur merge kar paoge.
- [ ] `Object.entries()` + `map()` combine karke objects pe iterate kar paoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`Object.freeze()` shallow hai** — nested objects tab bhi change ho sakte hain. (Chapter 03)
- **`Object.assign(target, src)` target ko mutate karta hai** — pehla argument badal jaata hai. Safe rehna hai to `{...a, ...b}` use karo. (Chapter 03)
- **Do objects kabhi `===` equal nahi hote** — `{} === {}` hamesha `false`, chahe content same ho. References compare hote hain, content nahi. (Chapter 01)
- **Dot vs bracket** — dynamic key ke liye bracket chahiye: `obj[keyVariable]`, `obj.keyVariable` nahi. (Chapter 01)

---

## Practice

- **Coding problems:** [28-Coding-Problems/02-Intermediate/](../28-Coding-Problems/02-Intermediate/01-Problems.md)
- **Projects:** [Expense Tracker](../27-Projects/Intermediate/02-Expense-Tracker/README.md), [Notes Application](../27-Projects/Intermediate/03-Notes-Application/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/04-Objects.md](../30-Revision/Cheat-Sheets/04-Objects.md)

---

**Shuru karo:** [01-Object-Basics.md](./01-Object-Basics.md)
**Agla Module:** [08-Scope-and-Closures](../08-Scope-and-Closures/00-Module-Overview.md)
