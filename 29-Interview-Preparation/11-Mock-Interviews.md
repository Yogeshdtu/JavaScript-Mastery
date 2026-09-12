# Interview Prep: Mock Interviews

**Module:** 29-Interview-Preparation
**Previous:** [10-Advanced-Questions.md](./10-Advanced-Questions.md)
**Next:** [30-Revision/Cheat-Sheets/](../30-Revision/Cheat-Sheets/)

---

## How to Use This Chapter

Ye 3 **full mock-interview simulations** hain — beginner, intermediate, aur advanced level ke liye. Har ek mein: warm-up questions, coding-round, aur behavioral/conceptual questions ka mix hai — bilkul real interview jaisa flow. **Time yourself** — try each mock in one sitting (30-45 minutes), answering out loud before checking answers.

---

## Mock Interview 1 — Beginner/Fresher Level (30 min)

**Round 1: Rapid-Fire Concepts (5 min)**
1. `let` vs `const` — kab kaunsa use karoge?
2. `typeof []` kya return karta hai?
3. Array se duplicate values kaise remove karoge?

*(Answers: [01-Basic-Questions.md](./01-Basic-Questions.md), [02-Core-JavaScript.md](./02-Core-JavaScript.md))*

**Round 2: Live Coding (15 min)**
"Ek function likho jo ek array of numbers le aur unka average return kare, invalid inputs (empty array) ko gracefully handle karte hue."
```js
function average(numbers) {
  if (!Array.isArray(numbers) || numbers.length === 0) return 0;
  const sum = numbers.reduce((total, n) => total + n, 0);
  return sum / numbers.length;
}
```
**Follow-up:** "Agar array mein non-number values ho to?" — discuss filtering/validating input first.

**Round 3: Conceptual (10 min)**
"Explain karo function declaration aur function expression mein difference, hoisting ke context mein." — see [03-Functions-and-Closures.md](./03-Functions-and-Closures.md), Q1 of the "core" chapter.

---

## Mock Interview 2 — Intermediate Level (40 min)

**Round 1: Rapid-Fire (5 min)**
1. `map`, `filter`, `reduce` mein difference?
2. Event delegation kya hai?
3. `Promise.all` vs `Promise.allSettled`?

*(Answers: [02-Core-JavaScript.md](./02-Core-JavaScript.md), [06-DOM-and-Browser.md](./06-DOM-and-Browser.md), [05-Async-JavaScript.md](./05-Async-JavaScript.md))*

**Round 2: Live Coding (20 min)**
"Ek debounce function implement karo from scratch, aur explain karo ek search-input ke context mein ye kyun useful hai."
```js
function debounce(fn, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}
```
**Follow-up:** "Iske aur throttle mein kya difference hai? Ek use-case do jaha throttle better ho." — see [22-Performance/03-Debounce-Throttle-Lazy-Loading.md](../22-Performance/03-Debounce-Throttle-Lazy-Loading.md).

**Round 3: Output Prediction (10 min)**
```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
```
Predict, then explain the event-loop mechanism step-by-step — see [08-Output-Based-Questions.md](./08-Output-Based-Questions.md).

**Round 4: Behavioral/Design (5 min)**
"Tumne kabhi ek bug fix kiya jo memory-leak ki wajah se tha? Kaise identify/fix kiya?" — reference [22-Performance/02-Memory-and-Garbage-Collection.md](../22-Performance/02-Memory-and-Garbage-Collection.md) concepts even if hypothetically answering.

---

## Mock Interview 3 — Advanced/Senior Level (45 min)

**Round 1: Deep Conceptual (10 min)**
"Explain the Event Loop completely — macrotasks, microtasks, and their exact interaction order. Then walk through this snippet step-by-step":
```js
setTimeout(() => {
  console.log("Timeout 1");
  Promise.resolve().then(() => console.log("Nested Promise"));
}, 0);
Promise.resolve().then(() => console.log("Promise 1"));
console.log("Sync");
```
*(See [15-Event-Loop/03-Event-Loop-Step-By-Step-Examples.md](../15-Event-Loop/03-Event-Loop-Step-By-Step-Examples.md) for the exact tracing method expected in a strong answer.)*

**Round 2: System-Design-Adjacent Coding (20 min)**
"Design a simple in-memory rate-limiter for an API — implement it, and explain the trade-offs of your approach vs. alternatives."
- Expected: A working `createRateLimiter` implementation (see [28-Coding-Problems/Solutions/03-Advanced-Solutions.md](../28-Coding-Problems/Solutions/03-Advanced-Solutions.md)), PLUS a verbal discussion of in-memory vs. distributed (Redis-based) rate-limiting for multi-server deployments.

**Round 3: Architecture/Security Discussion (10 min)**
"Ek authentication-system design karo conceptually — JWT ya session-based? Tokens kaha store karoge? SQL injection kaise prevent karoge?"
- Expected: Discussion covering [19-Backend-JavaScript/03-Authentication-and-Validation.md](../19-Backend-JavaScript/03-Authentication-and-Validation.md), [23-Security/03-Auth-and-Token-Security.md](../23-Security/03-Auth-and-Token-Security.md), and [20-Databases/04-Database-Security.md](../20-Databases/04-Database-Security.md).

**Round 4: Code Review Simulation (5 min)**
Given this snippet, identify issues:
```js
app.get("/user/:id", (req, res) => {
  const query = `SELECT * FROM users WHERE id = ${req.params.id}`;
  db.query(query, (err, result) => {
    res.json(result);
  });
});
```
**Expected findings:** (1) SQL Injection risk (string concatenation — [20-Databases/04-Database-Security.md](../20-Databases/04-Database-Security.md)), (2) no error-handling for `err`, (3) no authentication/authorization check, (4) returns raw DB result which may include sensitive fields ([23-Security/04-Dependency-and-Data-Exposure.md](../23-Security/04-Dependency-and-Data-Exposure.md)).

---

## Final Tips for Real Interviews

1. **Think out loud** — interviewers evaluate your reasoning process, not just the final answer.
2. **Ask clarifying questions** before coding — "Should I handle edge cases like empty arrays?"
3. **State time/space complexity** when relevant, even if not asked.
4. **It's OK to say "I don't know, but here's my reasoning"** — better than guessing confidently and being wrong.
5. Review [30-Revision/JavaScript-Gotchas.md](../30-Revision/JavaScript-Gotchas.md) and [30-Revision/Final-Revision-Roadmap.md](../30-Revision/Final-Revision-Roadmap.md) the night before a real interview.

---

**Interview Preparation Complete!** Next: [30-Revision/Cheat-Sheets/](../30-Revision/Cheat-Sheets/) for quick-reference material.
