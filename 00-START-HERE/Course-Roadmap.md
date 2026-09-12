# Course Roadmap

**Module:** 00-START-HERE
**Previous:** [README.md](./README.md)
**Next:** [How-to-Study.md](./How-to-Study.md)

Ye document tumhe **pura learning journey** ek bird's-eye view mein dikhata hai — kaha se shuru hoge, kaha tak pahunchoge, aur har stage pe kya expect karna hai.

---

## The Complete Progression

```text
Programming Basics
        ↓
JavaScript Fundamentals        (Modules 01-04)
        ↓
Core JavaScript                (Modules 05-09)
        ↓
Functions & Objects Mastery    (Modules 05-07 deep)
        ↓
Advanced Language Concepts     (Modules 08-10)
        ↓
DOM & Browser                  (Modules 11-12)
        ↓
Async JavaScript               (Modules 13-15)
        ↓
APIs & Networking              (Module 16)
        ↓
Modules & Node.js              (Modules 17-18)
        ↓
Backend Development            (Modules 19-20)
        ↓
Testing, Performance, Security (Modules 21-23)
        ↓
Advanced Internals              (Module 24)
        ↓
Design Patterns & Professional  (Modules 25-26)
        ↓
Projects                        (Module 27, ongoing)
        ↓
Interview Preparation           (Module 29)
        ↓
Professional JavaScript Developer
```

---

## Stage-by-Stage Breakdown

### Stage 1 — JavaScript Fundamentals (Modules 01-04)

**Tum kya jaanoge:** JavaScript kya hai, kaise run hota hai, variables, data types, operators, aur control flow (if/else, loops).

**Prerequisites:** Koi bhi nahi. Zero se shuru.

**Aage kya:** Functions — jo JavaScript ka core building block hai.

**Projects:** Calculator, Number Guessing Game.

---

### Stage 2 — Core JavaScript (Modules 05-09)

**Tum kya jaanoge:** Functions, closures, recursion, arrays ke saare methods, objects, destructuring, scope, `this`, aur type coercion (`==` vs `===` ka sach).

**Prerequisites:** Stage 1 complete.

**Aage kya:** Modern JavaScript syntax aur patterns (ES6+).

**Projects:** Quiz App, To-Do App, Digital Clock, Unit Converter, Form Validation App.

> Ye sabse important stage hai. Zyadatar interview questions aur real bugs isi stage ke concepts (`this`, closures, coercion) se aate hain. Yahan jaldi mat karo.

---

### Stage 3 — Advanced Language Concepts (Module 10)

**Tum kya jaanoge:** Classes, prototypes aur inheritance, Map/Set, generators, symbols, private fields — matlab "modern JavaScript" jo aaj real codebases mein likha jaata hai.

**Prerequisites:** Stage 2 complete, especially closures aur `this`.

**Aage kya:** Browser JavaScript — DOM manipulation.

---

### Stage 4 — DOM & Browser (Modules 11-12)

**Tum kya jaanoge:** Webpage ko JavaScript se control karna — elements select/create/modify karna, events handle karna, forms validate karna, browser storage use karna.

**Prerequisites:** Functions, objects, arrays (Stage 2).

**Aage kya:** Asynchronous JavaScript — kyunki DOM events aur network requests async hote hain.

**Projects:** Digital Clock, Form Validation App, Notes Application.

---

### Stage 5 — Async JavaScript (Modules 13-15)

**Tum kya jaanoge:** Callbacks, Promises, async/await, aur sabse important — **Event Loop** ka internal working. Ye JavaScript ka sabse confusing lekin sabse critical part hai.

**Prerequisites:** Functions, closures achi tarah aani chahiye.

**Aage kya:** Real APIs se data fetch karna.

**Projects:** Weather App, Movie Search Application.

---

### Stage 6 — APIs & Networking (Module 16)

**Tum kya jaanoge:** HTTP kaise kaam karta hai, REST APIs, Fetch API, authentication concepts, CORS.

**Prerequisites:** Stage 5 (Promises/async-await) complete.

**Aage kya:** Code ko modules mein organize karna, phir server-side JavaScript.

---

### Stage 7 — Modules & Node.js (Modules 17-18)

**Tum kya jaanoge:** ES Modules vs CommonJS, Node.js runtime, file system, streams, environment variables — matlab browser se bahar JavaScript.

**Prerequisites:** Stages 1-6.

**Aage kya:** Actual backend applications banana.

---

### Stage 8 — Backend Development (Modules 19-20)

**Tum kya jaanoge:** HTTP servers, routing, middleware, REST API design, authentication, databases (SQL/NoSQL), ORMs.

**Prerequisites:** Node.js basics (Module 18).

**Aage kya:** Apna code test aur debug karna professionally.

**Projects:** REST API, Authentication System, E-commerce Backend.

---

### Stage 9 — Testing, Performance, Security (Modules 21-23)

**Tum kya jaanoge:** Unit/integration testing, mocking, debugging techniques, Big O basics, memory/garbage collection, XSS/CSRF jaise security vulnerabilities.

**Prerequisites:** Backend basics helpful but not mandatory for testing/debugging.

**Aage kya:** Engine-level internals — "under the hood" JavaScript.

---

### Stage 10 — Advanced Internals (Module 24)

**Tum kya jaanoge:** Execution contexts, prototype chain internals, Proxy/Reflect, WeakRefs, garbage collection concepts — professional/expert level depth.

**Prerequisites:** Modules 08 (Scope/Closures) aur 10 (Modern JS) achi tarah clear hone chahiye.

**Aage kya:** Design patterns aur professional practices.

---

### Stage 11 — Design Patterns & Professional JavaScript (Modules 25-26)

**Tum kya jaanoge:** Factory, Singleton, Observer, Pub/Sub jaise design patterns; code quality, professional workflow, modern vs legacy JS ka clear summary.

**Prerequisites:** Stages 1-10.

---

### Stage 12 — Projects (Module 27, ongoing throughout)

Projects **parallel** mein complete karte raho — sirf end mein nahi. Har stage complete hone ke baad relevant project try karo (dekho [README.md](./README.md) ka "Projects Per Stage" table).

---

### Stage 13 — Interview Preparation (Module 29)

**Tum kya jaanoge:** Structured interview questions har topic pe, output-prediction questions, coding rounds, aur mock interview simulations.

**Prerequisites:** Modules 01-26 complete (ya kam se kam 01-18).

---

### Stage 14 — Revision (Module 30, ongoing forever)

Ye module **khatam nahi hota**. Cheat sheets aur quick-revision notes tum months/years baad bhi use karoge jab kisi concept ko fatafat refresh karna ho.

---

### Bonus Stage — TypeScript Bridge & Build Tools (Modules 31-32)

**Ye mandatory spine (01-30) ka hissa nahi hai** — ye do **bonus modules** hain jo "JavaScript jaanna" se "professional, job-ready JavaScript/TypeScript developer" ki taraf ka pul (bridge) hain.

**Tum kya jaanoge:**
- Module 31: TypeScript kya hai, basic types, interfaces, generics, aur ek existing JS project ko TypeScript mein migrate karna.
- Module 32: Build step kyun hota hai (Babel/transpilation), bundlers (Webpack/Vite) kaise kaam karte hain, aur ESLint/Prettier/npm-scripts se professional workflow kaisa dikhta hai.

**Prerequisites:** Module 19 (Backend JavaScript) tak ka comfortable knowledge — ideally functions, objects, classes, aur ES Modules (Module 17) clear hone chahiye.

**Kab padhein:** Kabhi bhi Module 19 ke baad, Module 27 (Projects) se pehle ya usके parallel. In dono modules ke concepts (TypeScript, bundlers) tum Advanced Projects (Module 27) mein real-world tools ke saath jodh sakte ho.

**Kyun zaroori hai:** Zyadatar modern job postings "JavaScript/TypeScript" combined maangte hain, aur har professional codebase ek build step (bundler + linter) use karti hai — ye do modules is gap ko cover karte hain jo pure-JavaScript-language mastery se aage ka, ecosystem-level knowledge hai.

---

## Final State

Is roadmap ko complete karne ke baad tum:

- JavaScript ko **beginner se professional level** tak samajhte ho.
- Frontend (DOM, events, async) aur backend (Node.js, APIs, databases) dono handle kar sakte ho.
- Interviews mein confidently "why" explain kar sakte ho, sirf syntax nahi.
- Ek **permanent reference library** rakhte ho jo har naye project/interview mein use hogi.

---

**Next:** [How-to-Study.md](./How-to-Study.md)
