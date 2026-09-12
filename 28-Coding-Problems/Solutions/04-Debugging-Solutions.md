# Debugging Solutions

**Back to problems:** [04-Debugging/01-Problems.md](../04-Debugging/01-Problems.md)

---

### Solution 1: The Loop Variable Bug
**Bug:** `var` is function-scoped, so all 3 closures share the SAME `i`, which is `3` by the time the functions are called.
**Actual output:** `3, 3, 3`
**Fix:**
```js
for (let i = 0; i < 3; i++) { // 'let' creates a NEW binding per iteration (Module 08, ch 06)
  buttons.push(function () { console.log(i); });
}
```

### Solution 2: The Async Loop
**Bug:** `forEach` doesn't wait for async callbacks — it fires all iterations immediately without awaiting them, so `return users` executes before any `await fetchUser(id)` completes.
**Fix:**
```js
async function fetchAllUsers(ids) {
  const users = await Promise.all(ids.map(id => fetchUser(id))); // Module 13, ch 04 + Module 14, ch 03
  return users;
}
```

### Solution 3: The Mutation Surprise
**Bug:** `cart` is a reference type (Module 02, chapter 05) — `addItem` mutates the original object's nested array directly, even though it "looks like" a new cart is being returned.
**Actual output:** `["apple"]` (NOT `[]`)
**Fix:** Create a new object/array instead of mutating:
```js
function addItem(cart, item) {
  return { ...cart, items: [...cart.items, item] }; // Module 07, ch 02's spread — no mutation
}
```

### Solution 4: The `this` Confusion
**Bug:** Regular `function` passed to `setInterval` loses `this` — it's called as a plain function, so `this` is NOT `timer` (Module 08, chapter 05).
**Actual output:** `NaN` repeatedly, or a `TypeError` in strict mode.
**Fix:**
```js
start() {
  setInterval(() => { // Arrow function inherits 'this' lexically (Module 08, ch 05)
    this.seconds++;
    console.log(this.seconds);
  }, 1000);
}
```

### Solution 5: The Equality Trap
**Bug:** `user.discount == null` is `false` for `discount: 0` (since `0 != null`), so the function correctly proceeds to `return user.discount > 0`, which is `false` for `0`. **Actually — this one isn't buggy!** It correctly returns `false` for a `0` discount. This problem tests whether you correctly trace through the logic rather than assuming a bug exists.

### Solution 6: The NaN Check
**Bug:** `NaN !== NaN` is always `true` (Module 09, chapter 03) — so the check `age !== NaN` is ALWAYS true, regardless of the actual value.
**Fix:**
```js
function validateAge(age) {
  if (!Number.isNaN(age)) { // Module 09, chapter 01's correct NaN-check
    return "Valid age";
  }
  return "Invalid age";
}
```

### Solution 7: The Array Sort Bug
**Bug:** `.sort()` without a compare-function sorts lexicographically (as strings) — `"10"` comes before `"2"` (Module 06, chapter 02).
**Actual output:** `[10, 2, 33, 4]` (unchanged order, since "10" < "2" < "33" < "4" as strings)
**Fix:**
```js
scores.sort((a, b) => a - b); // Numeric compare function
```

### Solution 8: The Promise That Never Resolves
**Bug:** If `ms <= 0`, `resolve` is never called — the promise stays `pending` forever.
**Fix:**
```js
function delay(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, Math.max(ms, 0)); // Always call setTimeout, clamping negative values to 0
  });
}
```

### Solution 9: The Object Comparison
**Bug:** `===` on objects checks reference-equality (Module 02, chapter 05), not content — `{}` is never `===` to another `{}`, even an empty one.
**Fix:**
```js
function isCartEmpty(cart) {
  return Object.keys(cart).length === 0; // Module 07, chapter 03
}
```

### Solution 10: The Missing Return in a Reducer
**Bug:** The arrow-function body uses `{ }` (a block body), which requires an explicit `return` — without it, the callback returns `undefined` every time, corrupting the accumulator (Module 05, chapter 02).
**Actual output:** `NaN` (since `undefined + item.price` is `NaN`)
**Fix:**
```js
function sumPrices(items) {
  return items.reduce((total, item) => {
    return total + item.price; // Explicit return needed with block-body arrow functions!
  }, 0);
  // Or simpler: items.reduce((total, item) => total + item.price, 0) — implicit return, no braces
}
```

---

**Back to problems:** [04-Debugging/01-Problems.md](../04-Debugging/01-Problems.md)
