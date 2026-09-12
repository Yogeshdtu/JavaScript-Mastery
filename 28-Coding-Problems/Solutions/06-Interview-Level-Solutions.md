# Interview-Level Solutions

**Back to problems:** [06-Interview-Level/01-Problems.md](../06-Interview-Level/01-Problems.md)

---

### Solution 1: Two Sum
```js
function twoSum(nums, target) {
  const seen = new Map(); // value -> index
  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];
    if (seen.has(complement)) {
      return [seen.get(complement), i];
    }
    seen.set(nums[i], i);
  }
  return [];
}
```
**Why O(n):** Each element is checked against a hash-map lookup (O(1)) instead of a nested loop (O(n)) — Module 22, chapter 01's Big-O optimization pattern.

### Solution 2: Valid Parentheses
```js
function isValid(s) {
  const stack = [];
  const pairs = { ")": "(", "]": "[", "}": "{" };
  for (const char of s) {
    if (char === "(" || char === "[" || char === "{") {
      stack.push(char);
    } else {
      if (stack.pop() !== pairs[char]) return false;
    }
  }
  return stack.length === 0;
}
```

### Solution 3: Debounce and Throttle (see Module 22, chapter 03 for full implementations)
Practice explaining: "Debounce waits for a pause in activity before firing (search-inputs). Throttle fires at most once per interval during continuous activity (scroll-tracking)." Live-code both from memory.

### Solution 4: Flatten and Deduplicate
```js
function flattenAndDedupe(arr) {
  return [...new Set(arr.flat(Infinity))];
}
```

### Solution 5: Module Pattern (Data Privacy)
```js
const counterModule = (function () {
  let count = 0; // Truly private — inaccessible from outside
  return {
    increment() { return ++count; },
    reset() { count = 0; }
  };
})();
```
**Talking point:** Mention that modern code would use ES Modules (Module 17) or class private fields (`#count`, Module 10 chapter 07) instead of this legacy IIFE pattern.

### Solution 6: Event Delegation
```js
document.getElementById("list").addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    console.log("Clicked:", event.target.textContent);
  }
});
// New <li> elements added later work automatically — no re-binding needed!
```

### Solution 7: Cache with Expiration
```js
class ExpiringCache {
  #store = new Map();

  set(key, value, ttlMs) {
    this.#store.set(key, { value, expiresAt: Date.now() + ttlMs });
  }

  get(key) {
    const entry = this.#store.get(key);
    if (!entry) return undefined;
    if (Date.now() > entry.expiresAt) {
      this.#store.delete(key);
      return undefined;
    }
    return entry.value;
  }
}
```

### Solution 8: Explain the Event Loop
Practice narrating: "First, all synchronous code runs to completion. Then, the ENTIRE microtask queue drains — including new microtasks added during draining. Only then does ONE macrotask run, followed by another full microtask-drain, and the cycle repeats." Reference Module 15, chapter 03's step-by-step trace method.

### Solution 9: Observer Pattern
```js
class Subject {
  #observers = [];
  subscribe(fn) { this.#observers.push(fn); }
  unsubscribe(fn) { this.#observers = this.#observers.filter(o => o !== fn); }
  notify(data) { this.#observers.forEach(fn => fn(data)); }
}
```

### Solution 10: Three Ways to Empty an Array
```js
let arr = [1, 2, 3];
let ref = arr;

arr.length = 0;              // Mutates in place — 'ref' also sees [] (Module 06, chapter 01)
// vs.
arr = [];                       // Creates a NEW array — 'ref' still points to the OLD (now-unrelated) array
// vs.
const removed = arr.splice(0, arr.length); // Mutates in place, AND returns the removed items
```

---

**Back to problems:** [06-Interview-Level/01-Problems.md](../06-Interview-Level/01-Problems.md)
