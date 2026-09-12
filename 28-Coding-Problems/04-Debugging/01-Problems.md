# Debugging Problems

**Module:** 28-Coding-Problems
**Previous:** [03-Advanced/01-Problems.md](../03-Advanced/01-Problems.md)
**Solutions:** [Solutions/04-Debugging-Solutions.md](../Solutions/04-Debugging-Solutions.md)

---

Har problem mein ek intentional bug hai. Bug find karo, explain karo **kyun** ye bug hai, aur fix karo.

### Problem 1: The Loop Variable Bug
```js
function createButtons() {
  const buttons = [];
  for (var i = 0; i < 3; i++) {
    buttons.push(function () { console.log(i); });
  }
  return buttons;
}
const btns = createButtons();
btns[0](); btns[1](); btns[2]();
// Expected: 0, 1, 2 — Actual: ???
```
**Concepts Tested:** Closures, `var` vs `let` (Module 08)

### Problem 2: The Async Loop
```js
async function fetchAllUsers(ids) {
  const users = [];
  ids.forEach(async (id) => {
    const user = await fetchUser(id); // Assume fetchUser is a working async function
    users.push(user);
  });
  return users; // Bug: this doesn't work as expected!
}
```
**Concepts Tested:** Async/await, array methods (Module 14)

### Problem 3: The Mutation Surprise
```js
function addItem(cart, item) {
  cart.items.push(item);
  return cart;
}
const originalCart = { items: [] };
const newCart = addItem(originalCart, "apple");
console.log(originalCart.items); // Expected: [] — Actual: ???
```
**Concepts Tested:** Reference types, mutation (Module 02, chapter 05)

### Problem 4: The `this` Confusion
```js
const timer = {
  seconds: 0,
  start() {
    setInterval(function () {
      this.seconds++;
      console.log(this.seconds);
    }, 1000);
  }
};
timer.start(); // Expected: 1, 2, 3... — Actual: ???
```
**Concepts Tested:** `this` binding (Module 08, chapter 05)

### Problem 5: The Equality Trap
```js
function hasDiscount(user) {
  if (user.discount == null) { // Intentional trap in the CONDITION, not the value
    return false;
  }
  return user.discount > 0;
}
console.log(hasDiscount({ discount: 0 })); // What's the bug in the LOGIC, not the syntax?
```
**Concepts Tested:** Truthy/falsy, equality (Module 09)

### Problem 6: The NaN Check
```js
function validateAge(age) {
  if (age !== NaN) { // This check has a fundamental problem
    return "Valid age";
  }
  return "Invalid age";
}
console.log(validateAge(NaN)); // Expected: "Invalid age" — Actual: ???
```
**Concepts Tested:** NaN comparison (Module 09, chapter 03)

### Problem 7: The Array Sort Bug
```js
const scores = [10, 2, 33, 4];
scores.sort();
console.log(scores); // Expected: [2, 4, 10, 33] — Actual: ???
```
**Concepts Tested:** Array sort (Module 06, chapter 02)

### Problem 8: The Promise That Never Resolves
```js
function delay(ms) {
  return new Promise((resolve) => {
    if (ms > 0) {
      setTimeout(resolve, ms);
    }
    // Bug: what happens if ms <= 0?
  });
}
delay(0).then(() => console.log("Done")); // Never logs!
```
**Concepts Tested:** Promises (Module 13)

### Problem 9: The Object Comparison
```js
function isCartEmpty(cart) {
  return cart === {}; // This will NEVER be true, no matter what!
}
console.log(isCartEmpty({})); // Expected: true — Actual: false
```
**Concepts Tested:** Reference equality (Module 02, chapter 05)

### Problem 10: The Missing Return in a Reducer
```js
function sumPrices(items) {
  return items.reduce((total, item) => {
    total + item.price; // Bug: missing something critical
  }, 0);
}
console.log(sumPrices([{ price: 10 }, { price: 20 }])); // Expected: 30 — Actual: ???
```
**Concepts Tested:** Array reduce, arrow function syntax (Module 06, chapter 03; Module 05, chapter 02)

---

**Next:** [05-Output-Prediction/01-Problems.md](../05-Output-Prediction/01-Problems.md) | **Solutions:** [Solutions/04-Debugging-Solutions.md](../Solutions/04-Debugging-Solutions.md)
