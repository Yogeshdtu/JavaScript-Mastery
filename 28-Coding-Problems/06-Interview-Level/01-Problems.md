# Interview-Level Problems

**Module:** 28-Coding-Problems
**Previous:** [05-Output-Prediction/01-Problems.md](../05-Output-Prediction/01-Problems.md)
**Solutions:** [Solutions/06-Interview-Level-Solutions.md](../Solutions/06-Interview-Level-Solutions.md)

---

### Problem 1: Two Sum
**Difficulty:** 🟡 (Classic FAANG-style opener)
**Concepts Tested:** Arrays, objects (Module 06-07)
**Problem:** Diye gaye array aur target-sum ke liye, do indices return karo jinke values ka sum target ke barabar ho.
**Expected Approach:** Use a hash-map (object) to store seen-values and their indices — O(n) instead of naive O(n²) nested loops.

### Problem 2: Valid Parentheses
**Difficulty:** 🟡
**Concepts Tested:** Arrays as a stack (Module 06)
**Problem:** Ek string of brackets `()[]{}` valid hai ya nahi check karo (properly matched and nested).
**Expected Approach:** Use an array as a stack — push opening brackets, pop and match on closing brackets.

### Problem 3: Implement `debounce` AND `throttle` and Explain the Difference Live
**Difficulty:** 🔴
**Concepts Tested:** Closures, real-world performance (Module 22, chapter 03)
**Problem:** Interviewer often asks candidates to implement BOTH and explain when to use each — practice explaining out loud, not just coding.
**Expected Approach:** Revisit Module 22, chapter 03's implementations — practice explaining the "why" for each use-case.

### Problem 4: Flatten and Deduplicate
**Difficulty:** 🟡
**Concepts Tested:** Arrays, Set (Module 06, 10)
**Problem:** Ek function likho jo nested arrays ko flatten kare AND duplicates remove kare, ek single pass mein (as much as possible).
**Expected Approach:** `flat(Infinity)` followed by `new Set()`, or a manual recursive approach with a Set for tracking seen values.

### Problem 5: Explain and Implement the Module Pattern
**Difficulty:** 🟡
**Concepts Tested:** Closures, encapsulation (Module 05, chapter 07)
**Problem:** Interviewer: "Explain data-privacy in JavaScript without classes." Implement a counter-module with private state.
**Expected Approach:** IIFE returning an object with methods that close over private variables (Module 05, chapter 06-07).

### Problem 6: Explain Event Delegation and Implement It
**Difficulty:** 🟡
**Concepts Tested:** Events, bubbling (Module 12)
**Problem:** Ek dynamic list banao (items add/remove ho sakte hain) with a single delegated click-listener, explain why delegation is better here.
**Expected Approach:** Attach one listener to the parent, check `event.target` to determine which child was clicked (Module 12, chapter 02).

### Problem 7: Design a Simple Cache with Expiration
**Difficulty:** 🔴
**Concepts Tested:** Closures, Map, timers (Module 08, 10, 13)
**Problem:** Ek `Cache` class banao jisme `set(key, value, ttlMs)` aur `get(key)` ho — values automatically "expire" ho jaayein `ttlMs` ke baad.
**Expected Approach:** Store `{ value, expiresAt }` per key; check `Date.now() > expiresAt` on `get()` and evict if expired.

### Problem 8: Explain the Event Loop with a Code Example
**Difficulty:** 🔴
**Concepts Tested:** Event loop (Module 15)
**Problem:** Interviewer gives a mixed `setTimeout`/Promise snippet — predict output AND explain the mechanism step-by-step (not just the answer).
**Expected Approach:** Revisit Module 15, chapter 03's step-by-step tracing method — practice narrating it out loud.

### Problem 9: Implement a Simple Observer Pattern
**Difficulty:** 🟡
**Concepts Tested:** Design patterns (Module 25, chapter 02)
**Problem:** Build a `Subject` class with `subscribe`/`unsubscribe`/`notify` — a very common "explain a design pattern" interview question.
**Expected Approach:** Revisit Module 25, chapter 02's Observer implementation from scratch, without looking.

### Problem 10: Explain Why `array.length = 0` Clears an Array
**Difficulty:** 🟡 (conceptual/verbal question)
**Concepts Tested:** Arrays internals (Module 06, chapter 01)
**Problem:** Interviewer: "What are 3 ways to empty an array, and what's different about each?"
**Expected Approach:** Compare `arr.length = 0` (mutates, all references see it empty), `arr = []` (creates new reference, old references keep old data), `arr.splice(0, arr.length)` (mutates, returns removed items).

---

**Next:** [07-Real-World-Problems/01-Problems.md](../07-Real-World-Problems/01-Problems.md) | **Solutions:** [Solutions/06-Interview-Level-Solutions.md](../Solutions/06-Interview-Level-Solutions.md)
