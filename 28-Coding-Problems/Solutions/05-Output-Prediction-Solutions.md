# Output-Prediction Solutions

**Back to problems:** [05-Output-Prediction/01-Problems.md](../05-Output-Prediction/01-Problems.md)

---

### Solution 1 — Hoisting
```
undefined
ReferenceError: Cannot access 'b' before initialization
```
**Why:** `var a` is hoisted and initialized to `undefined`. `let b` is hoisted but stays in the TDZ (Module 02, chapter 03) until its declaration line executes.

### Solution 2 — Closures
```
1
1
2
```
**Why:** Both `inc1` and `inc2` share the SAME closure over `count` (Module 08, chapter 04) — they're not independent counters, since both come from the SAME call to `outer()`.

### Solution 3 — `this`
```
Test
undefined
```
**Why:** `regular` is called as `obj.regular()`, so `this` = `obj` (implicit binding). `arrow` was defined using an arrow function, which uses lexical `this` from where it was DEFINED (the outer/module scope), not `obj` (Module 08, chapter 05).

### Solution 4 — Type Coercion
```
"11"
0
"531"
"81"
```
**Why:** `1 + "1"` → string concatenation → `"11"`. `1 - "1"` → `-` always coerces to numbers → `0`. `"5" + 3 + 1` → left-to-right: `"5"+3="53"`, then `"53"+1="531"`. `5 + 3 + "1"` → `5+3=8` (number), then `8+"1"="81"`. (Module 09, chapter 02's left-to-right evaluation rule.)

### Solution 5 — Event Loop
```
A
D
C
B
```
**Why:** Synchronous code (`A`, `D`) runs first. Then the microtask queue drains (`C`, from the Promise). Then the macrotask queue runs (`B`, from setTimeout) — Module 15, chapters 02-03.

### Solution 6 — Array Reference
```
[1, 2, 3, 4]
true
```
**Why:** `arr2 = arr1` copies the REFERENCE, not the array itself (Module 02, chapter 05) — both variables point to the same array in memory.

### Solution 7 — Prototypes
```
Hi, Zoya
false
true
```
**Why:** `greet` lives on `Person.prototype`, not directly on `p` — so `p.hasOwnProperty("greet")` is `false` (it's inherited, Module 10, chapter 03). `name` was set directly in the constructor via `this.name = name`, so it IS an own property.

### Solution 8 — Async/Await Ordering
```
3
1
4
2
```
**Why:** `console.log("3")` runs first (before `test()` is even called). Inside `test()`, code runs synchronously UNTIL `await` (`console.log("1")` happens here). Then `test()` "pauses," control returns to the caller, `console.log("4")` runs. Only after the synchronous code fully completes does the microtask (`console.log("2")`, the continuation after `await`) run (Module 14, chapter 01; Module 15).

### Solution 9 — Equality Chain
```
true
false
false
true
```
**Why:** `null == undefined` is a special-cased `true` (Module 09, chapter 03). `null === undefined` is `false` (different types). `NaN == NaN` is always `false`. `[1,2] == "1,2"` — the array is coerced to a string via `toString()` → `"1,2"`, which then equals the string → `true` (Module 09, chapter 04's object-to-primitive conversion).

### Solution 10 — Destructuring Defaults
```
Guest undefined
Aryan undefined
Guest 25
```
**Why:** The `= {}` default on the parameter handles the "no argument at all" case (Module 05, chapter 03). Within the destructured object, `name` has its own default (`"Guest"`), but `age` has no default, so it's `undefined` whenever not provided (Module 07, chapter 02).

---

**Back to problems:** [05-Output-Prediction/01-Problems.md](../05-Output-Prediction/01-Problems.md)
