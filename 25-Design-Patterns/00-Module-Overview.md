# Module 25 — Design Patterns

**Difficulty:** 🔴 Advanced
**Chapters:** 3
**Estimated time:** 4-6 hours
**Prerequisites:** [Module 10 (Modern JS)](../10-Modern-JavaScript/00-Module-Overview.md), [Module 08 (Closures)](../08-Scope-and-Closures/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Design patterns **common problems ke tested solutions** hain. Inhe naye se invent karne ki zarurat nahi — developers decades se inhe use aur refine kar rahe hain.

Ye seekhne ka asli fayda: jab tum kisi codebase mein "Observer pattern" dekhoge, turant samajh jaoge kya ho raha hai. Aur team meetings mein "yahan Factory use karte hain" kehna, poora paragraph samjhane se behtar hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Creational Patterns](./01-Creational-Patterns.md) | Factory, Singleton, Builder — objects banane ke tareeke |
| 02 | [Behavioral Patterns](./02-Behavioral-Patterns.md) | ⭐ Observer, Pub/Sub, Strategy, Command, State |
| 03 | [Structural Patterns](./03-Structural-Patterns.md) | Module, Adapter, Decorator, Composition |

---

## Is Module Ke Baad Tum

- [ ] Common patterns ko code mein **pehchan** paoge.
- [ ] Observer/Pub-Sub khud implement kar paoge (interview favourite).
- [ ] Samajh jaoge "composition over inheritance" ka matlab kya hai.
- [ ] Sahi pattern choose kar paoge, har jagah class thopne ke bajaye.

---

## Sabse Zyada Use Hone Wale Patterns

| Pattern | Kahan dikhega |
|---------|---------------|
| **Observer / Pub-Sub** | Event systems, React state, Node EventEmitter |
| **Factory** | Different types ke objects ek jagah se banana |
| **Singleton** | Database connection, app config |
| **Module** | Private state with public API (closures se) |
| **Strategy** | Payment methods, sorting algorithms swap karna |

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **Patterns ko force mat karo** — pattern ek solution hai, goal nahi. Agar simple function kaam kar raha hai, usse pattern mein wrap karne ki zarurat nahi. Over-engineering asli risk hai. (Chapter 01)
- **Singleton ko overuse karna** — ye basically ek global state hai, aur testing mushkil bana deta hai. Soch samajh ke use karo. (Chapter 01)
- **Observer aur Pub/Sub thode alag hain** — Observer mein subject seedha observers ko jaanta hai; Pub/Sub mein ek middle "event bus" hota hai, publisher aur subscriber ek dusre ko nahi jaante. (Chapter 02)
- **JavaScript mein classes zaroori nahi** — closures aur plain objects se bhi zyadatar patterns implement hote hain, aksar zyada simply. (Chapter 03)

---

## Practice

- **Projects:** [Notification System](../27-Projects/Advanced/04-Notification-System/README.md) — Observer pattern ka real use. [Dashboard](../27-Projects/Intermediate/06-Dashboard/README.md) — Module pattern.
- **Coding problems:** [28-Coding-Problems/07-Real-World-Problems/](../28-Coding-Problems/07-Real-World-Problems/01-Problems.md)

---

**Shuru karo:** [01-Creational-Patterns.md](./01-Creational-Patterns.md)
**Agla Module:** [26-Professional-JavaScript](../26-Professional-JavaScript/00-Module-Overview.md)
