# Intermediate Coding Problems

**Module:** 28-Coding-Problems
**Previous:** [01-Beginner/01-Problems.md](../01-Beginner/01-Problems.md)
**Solutions:** [Solutions/02-Intermediate-Solutions.md](../Solutions/02-Intermediate-Solutions.md)

---

### Problem 1: Group Anagrams
**Difficulty:** 🟡
**Concepts Tested:** Objects, arrays, string manipulation (Module 06, 07)
**Problem:** Ek function `groupAnagrams(words)` likho jo words ke array ko anagram-groups mein organize kare.
**Expected Approach:** Sort each word's letters to create a "key" — words with the same sorted-key are anagrams. Group using an object.

### Problem 2: Flatten a Nested Array
**Difficulty:** 🟡
**Concepts Tested:** Recursion, arrays (Module 05, 06)
**Problem:** Ek function `flattenArray(arr)` likho jo deeply-nested array ko completely flat kare, bina `.flat(Infinity)` ke.
**Expected Approach:** Recursion — for each element, if it's an array, recursively flatten it; otherwise, add it directly.

### Problem 3: Debounce Implementation
**Difficulty:** 🟡
**Concepts Tested:** Closures, higher-order functions (Module 08, 22)
**Problem:** Apna khud ka `debounce(fn, delay)` function implement karo from scratch.
**Expected Approach:** Use `setTimeout` + `clearTimeout`, with the `timeoutId` persisted via a closure.

### Problem 4: Deep Clone an Object
**Difficulty:** 🟡
**Concepts Tested:** Objects, recursion (Module 07, 02)
**Problem:** Ek function `deepClone(obj)` likho jo nested objects/arrays ko poori tarah independently clone kare, bina `structuredClone()` ke.
**Expected Approach:** Recursively check if a value is an object/array — if so, recurse; otherwise, copy directly.

### Problem 5: Implement `Array.prototype.map` from Scratch
**Difficulty:** 🟡
**Concepts Tested:** Prototypes, higher-order functions (Module 10, 05)
**Problem:** Apna khud ka `myMap` method implement karo `Array.prototype` pe.
**Expected Approach:** `Array.prototype.myMap = function(callback) { ... }` — loop through `this`, apply the callback, collect results in a new array.

### Problem 6: Find the Missing Number
**Difficulty:** 🟡
**Concepts Tested:** Math, arrays
**Problem:** Ek array `[1..n]` hai jisme se ek number missing hai. Find it efficiently (better than O(n²)).
**Expected Approach:** Calculate expected sum via the formula `n*(n+1)/2`, subtract actual array-sum — the difference is the missing number.

### Problem 7: Memoization Wrapper
**Difficulty:** 🟡
**Concepts Tested:** Closures, Map (Module 08, 10)
**Problem:** Ek generic `memoize(fn)` function likho (Module 22, chapter 04 revisited) — apne words mein, without looking at the module.
**Expected Approach:** Use a `Map`/object as a cache, keyed by `JSON.stringify(args)`.

### Problem 8: Event Emitter from Scratch
**Difficulty:** 🟡
**Concepts Tested:** Classes, closures (Module 10, 18)
**Problem:** Apna khud ka simple `EventEmitter` class banao with `on()` and `emit()` methods, bina Node.js ke built-in wale ke.
**Expected Approach:** Store listeners in an object keyed by event-name (arrays of callbacks); `emit` loops through and calls them.

### Problem 9: Currying a Function
**Difficulty:** 🟡
**Concepts Tested:** Closures, higher-order functions (Module 08, 05)
**Problem:** Ek function `curry(fn)` likho jo kisi bhi function ko curried-version mein convert kare — `curry(add)(1)(2)(3)` should work like `add(1,2,3)`.
**Expected Approach:** Track collected arguments via closure; return a new function until enough arguments are collected (check `fn.length`).

### Problem 10: Promise.all from Scratch
**Difficulty:** 🟡
**Concepts Tested:** Promises (Module 13)
**Problem:** Apna khud ka `myPromiseAll(promises)` implement karo, matching `Promise.all()`'s behavior.
**Expected Approach:** Track resolved-count and results-array; resolve the outer promise when all inner promises resolve, reject immediately if any rejects.

---

**Next:** [03-Advanced/01-Problems.md](../03-Advanced/01-Problems.md) | **Solutions:** [Solutions/02-Intermediate-Solutions.md](../Solutions/02-Intermediate-Solutions.md)
