# Interview Prep: Advanced Questions

**Module:** 29-Interview-Preparation
**Previous:** [09-Coding-Questions.md](./09-Coding-Questions.md)
**Next:** [11-Mock-Interviews.md](./11-Mock-Interviews.md)

---

Senior/advanced-level questions — expect these in interviews for experienced roles.

**Q1: Explain the Event Loop in complete detail, including microtasks and macrotasks.**
A: (Full model answer) JavaScript is single-threaded with one Call Stack. Async operations (timers, network, DOM events) are handed off to Web APIs/libuv, which push their callbacks into either the Macrotask Queue (timers, events, I/O) or the Microtask Queue (Promises, async/await). The Event Loop's rule: whenever the Call Stack is empty, it FIRST fully drains the Microtask Queue (including any new microtasks added during draining) — only THEN does it process ONE macrotask, before repeating the cycle. This explains why `Promise.resolve().then()` always runs before `setTimeout(fn, 0)`. (Full detail: [15-Event-Loop/](../15-Event-Loop/))

**Q2: What is the difference between `Object.freeze()`, `Object.seal()`, and `Object.preventExtensions()`?**
A: `preventExtensions()` prevents adding new properties, but existing ones can still be modified/deleted. `seal()` additionally prevents deletion (but allows modification of existing values). `freeze()` is the strictest — prevents adding, deleting, AND modifying. All three are shallow. (Detail: [07-Objects/03-Object-Methods.md](../07-Objects/03-Object-Methods.md))

**Q3: Explain how JavaScript's garbage collector decides what to collect.**
A: Modern engines use a mark-and-sweep algorithm — starting from "roots" (global scope, currently-executing function's variables), the collector marks everything reachable via references. Anything unmarked (unreachable) is swept (freed). Closures keep specific variables alive by maintaining references to them even after their originating function has returned. Memory leaks occur when references are unintentionally kept alive (forgotten event listeners, uncleared timers). (Detail: [22-Performance/02-Memory-and-Garbage-Collection.md](../22-Performance/02-Memory-and-Garbage-Collection.md))

**Q4: What are `Proxy` and `Reflect`, and when would you use them?**
A: `Proxy` intercepts fundamental object operations (get, set, delete) via "traps," enabling validation, logging, and reactive-systems (like Vue's reactivity). `Reflect` provides the standardized way to invoke the default behavior for those same operations, typically used inside Proxy traps to correctly delegate to default behavior. (Detail: [24-Advanced-JavaScript-Internals/04-Proxy-and-Reflect.md](../24-Advanced-JavaScript-Internals/04-Proxy-and-Reflect.md))

**Q5: Explain V8's JIT compilation pipeline.**
A: Ignition (interpreter) generates bytecode for fast startup. As code runs repeatedly ("hot" code), TurboFan (optimizing compiler) compiles it into highly-optimized machine-code based on observed patterns (like consistent argument-types). If those assumptions break later (a type-change, for instance), the engine "deoptimizes," falling back to the generic path. (Detail: [24-Advanced-JavaScript-Internals/06-Event-Loop-and-Engine-Optimizations.md](../24-Advanced-JavaScript-Internals/06-Event-Loop-and-Engine-Optimizations.md))

**Q6: How would you prevent SQL Injection in a Node.js application?**
A: Always use parameterized queries (prepared statements) — never string-concatenate user-input directly into a query. The database driver treats parameters strictly as data, never as executable SQL, regardless of their content. ORMs typically handle this automatically. (Detail: [20-Databases/04-Database-Security.md](../20-Databases/04-Database-Security.md))

**Q7: What is "Composition over Inheritance," and why is it recommended?**
A: Instead of building deep class-hierarchies (`extends`), compose complex behavior from small, independent, reusable pieces (functions/objects). Deep inheritance is rigid (JavaScript only supports single inheritance) and prone to the "fragile base class" problem, where changing a parent unexpectedly breaks descendants. Composition offers more flexibility. (Detail: [25-Design-Patterns/03-Structural-Patterns.md](../25-Design-Patterns/03-Structural-Patterns.md))

**Q8: Explain the difference between `WeakMap` and `Map` in terms of garbage collection.**
A: A regular `Map` holds strong references to its keys — an object used as a key won't be garbage-collected even if no other references exist, as long as it remains in the Map. `WeakMap` holds keys weakly — if an object-key has no other references, it CAN be garbage-collected, and its `WeakMap` entry is automatically cleaned up. This makes `WeakMap` ideal for associating metadata with objects without preventing their cleanup. (Detail: [10-Modern-JavaScript/04-Map-Set-WeakMap-WeakSet.md](../10-Modern-JavaScript/04-Map-Set-WeakMap-WeakSet.md), [24-Advanced-JavaScript-Internals/05-WeakRefs-and-Garbage-Collection-Concepts.md](../24-Advanced-JavaScript-Internals/05-WeakRefs-and-Garbage-Collection-Concepts.md))

**Q9: How does `this` binding work with `call`, `apply`, `bind`, arrow functions, and regular function calls — rank their precedence.**
A: Priority order (highest to lowest): (1) `new` binding (constructor calls), (2) explicit binding (`call`/`apply`/`bind`), (3) implicit binding (`obj.method()`), (4) default binding (plain `fn()` call). Arrow functions don't participate in this system at all — they always use lexical `this` from their enclosing scope, regardless of how they're invoked. (Detail: [08-Scope-and-Closures/05-this-Keyword.md](../08-Scope-and-Closures/05-this-Keyword.md))

**Q10: What's the difference between authentication and authorization, and what is "Broken Access Control"?**
A: Authentication verifies identity ("who are you"). Authorization determines permissions ("what can you do"). Broken Access Control happens when an application checks authentication but forgets to verify ownership/permission for a specific resource — e.g., a user changing an ID in a URL to access someone else's data. It's consistently one of the most critical, common real-world vulnerabilities (OWASP Top 10). (Detail: [23-Security/03-Auth-and-Token-Security.md](../23-Security/03-Auth-and-Token-Security.md))

---

**Next:** [11-Mock-Interviews.md](./11-Mock-Interviews.md)
