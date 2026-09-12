# Error Handling in Async Code

**Module:** 14-Promises-and-Async-Await
**Difficulty:** 🔴 Advanced
**Previous:** [01-Async-Await-Basics.md](./01-Async-Await-Basics.md)
**Next:** [03-Sequential-vs-Parallel-Execution.md](./03-Sequential-vs-Parallel-Execution.md)

---

## 1. Learning Objectives

- `try/catch` se `async`/`await` code mein errors handle karna.
- Async error handling ke different patterns compare karna.
- Unhandled Promise rejections ka risk samajhna.

## 2. Prerequisites

[01-Async-Await-Basics.md](./01-Async-Await-Basics.md)

## 3. Concept in Simple Hinglish

Async/await code mein errors handle karne ke liye hum wahi purana, familiar **`try/catch`** syntax use karte hain jo synchronous code mein use hota hai — ye async/await ka ek bada advantage hai `.then()/.catch()` chains ke comparison mein, jaha error handling ka syntax alag hota tha.

## 4. Technical Explanation

Jab `await` ki gayi Promise **reject** hoti hai, `await` expression khud ek **error throw** karta hai (jaise `throw` statement) — isi wajah se normal `try/catch` block use karke usse pakda ja sakta hai.

## 5. Syntax

```js
async function myFunction() {
  try {
    const result = await someAsyncOperation();
    // use result
  } catch (error) {
    // handle error
  } finally {
    // cleanup, always runs
  }
}
```

## 6. Basic Examples

```js
function riskyOperation() {
  return new Promise((resolve, reject) => {
    setTimeout(() => reject(new Error("Something went wrong")), 500);
  });
}

async function run() {
  try {
    const result = await riskyOperation();
    console.log(result);
  } catch (error) {
    console.log("Caught:", error.message); // "Caught: Something went wrong"
  }
}

run();
```

## 7. Detailed Examples

**Handling multiple awaited operations with a single try/catch:**
```js
function getUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      id > 0 ? resolve({ id, name: "Meera" }) : reject(new Error("Invalid user ID"));
    }, 300);
  });
}
function getOrders(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => reject(new Error("Orders service is down")), 300);
  });
}

async function processUserOrders(id) {
  try {
    const user = await getUser(id);
    console.log("User:", user);

    const orders = await getOrders(user.id); // This one fails
    console.log("Orders:", orders); // Never reached

    return orders;
  } catch (error) {
    console.log("Error occurred:", error.message); // Catches error from EITHER getUser OR getOrders
    return null;
  }
}

processUserOrders(1);
// User: { id: 1, name: 'Meera' }
// Error occurred: Orders service is down
```
**Ek hi `try/catch`** poori chain ke andar kahi bhi hui error ko pakad leta hai — bilkul waise hi jaise synchronous code mein hota hai. Ye Promise-chain style ke `.catch()` jaisa hi centralized behavior hai, lekin zyada familiar syntax ke saath.

**Granular error handling — separate try/catch for different steps:**
```js
async function processWithGranularHandling(id) {
  let user;
  try {
    user = await getUser(id);
  } catch (error) {
    console.log("Failed to get user:", error.message);
    return; // Exit early — can't continue without a user
  }

  try {
    const orders = await getOrders(user.id);
    return orders;
  } catch (error) {
    console.log("Failed to get orders, using empty list:", error.message);
    return []; // Graceful fallback — continue with empty orders instead of failing completely
  }
}
```
Ye pattern useful hai jab different steps ke liye **different fallback strategies** chahiye ho — ek combined `try/catch` sab errors ko same tareeke se handle karta, jabki separate blocks fine-grained control dete hain.

**⚠️ The critical mistake — forgetting try/catch entirely (unhandled rejection):**
```js
async function forgotTryCatch() {
  const result = await riskyOperation(); // If this rejects, and there's no try/catch...
  console.log(result);
}

forgotTryCatch(); // Produces an "UnhandledPromiseRejection" — a real bug in production!
```
Agar `async` function ke andar koi `try/catch` nahi hai aur `await` ki gayi Promise reject ho jaati hai, error **poore application mein propagate ho sakti hai** aur agar caller bhi handle nahi karta, ye "Unhandled Promise Rejection" ban jaata hai — Node.js mein ye process ko crash bhi kar sakta hai (depending on configuration).

**Handling errors at the call site (alternative to try/catch inside):**
```js
async function fetchData() {
  const result = await riskyOperation(); // No try/catch here
  return result;
}

// Handle it where the function is CALLED, using .catch() on the returned promise:
fetchData().catch(error => console.log("Handled at call site:", error.message));
```
Ye ek valid alternative pattern hai — error handling ko function ke andar rakhne ke bajaye, function ko call karne wali jagah pe handle karna.

## 8. Mental Model

`try/catch` ke saath async/await ko socho ek **normal safety net** jaisa jo tum synchronous code mein bhi use karte ho — bas ab ye "future mein hone wali problems" (Promise rejections) ko bhi usi tarah pakad leta hai jaise "abhi hone wali problems" (synchronous throws) ko pakadta hai.

## 9. What Happens Internally?

Jab `await` ek rejected Promise ko encounter karta hai, engine internally us rejection ko ek `throw` statement mein convert kar deta hai us specific point pe — jisse normal JavaScript error-propagation mechanism (`try/catch` searching up the call stack) apply ho jaata hai, bilkul synchronous errors ki tarah.

## 10. Common Mistakes

- `async` function ke andar `try/catch` completely bhool jaana.
- `.catch()` (Promise-chain style) aur `try/catch` (async/await style) ko mix karke confusing code likhna.
- `finally` block ke andar `return` likhna jo `try`/`catch` ke return value ko silently override kar deta hai (Module 01, chapter 06 ke similar gotcha).

## 11. Edge Cases

```js
async function example() {
  try {
    return await riskyOperation();
  } finally {
    console.log("This runs whether it succeeds or fails");
  }
}
```
`return await` (bajaye sirf `return`) yahan important hai — agar tum `return riskyOperation();` likho (bina `await`), aur us Promise mein error ho, wo error is `try` block ke andar catch nahi hogi (kyunki function turant Promise return kar dega, wait nahi karega) — is subtle case mein `await` explicitly likhna zaroori hai error ko locally catch karne ke liye.

## 12. Real-World Usage

Production applications mein **every** async operation (API calls, database queries, file operations) ko proper error handling ke saath wrap kiya jaata hai — user ko meaningful error messages dikhane ke liye, aur application crash hone se bachane ke liye.

## 13. Comparison With Related Concepts

| Approach | Syntax Style | Best For |
|----------|-----------------|--------------|
| `try/catch` (with async/await) | Synchronous-feeling | Most modern code |
| `.catch()` (Promise chains) | Functional chaining | When staying in Promise-chain style |

## 14. Practice Questions

1. `await` ki gayi Promise reject hone pe kya hota hai (mechanism ke terms mein)?
2. Ek `async` function mein `try/catch` na hone se kya risk hai?
3. `finally` block async functions mein kab chalta hai?

## 15. Challenge

Ek `async function safeDivide(a, b)` likho jo agar `b === 0` ho to ek Error throw kare (`throw new Error("Cannot divide by zero")`), aur ise call karne wale code mein `try/catch` se handle karo, error message ko gracefully display karte hue.

## 16. Interview Questions

**Q1: `async`/`await` mein errors kaise handle kiye jaate hain?**
A: `async`/`await` ke saath, standard synchronous `try/catch` syntax use hoti hai. Jab `await` ki gayi Promise reject hoti hai, wo rejection automatically ek thrown error mein convert ho jaati hai us point pe, jisse tumhara `try/catch` block usse normal JavaScript error ki tarah pakad sakta hai. Ye Promise-chain-style `.catch()` ka ek zyada familiar, synchronous-feeling alternative hai, aur multiple sequential `await` calls ko ek hi `try/catch` mein wrap karke centralized error handling bhi mil jaati hai.

**Q2: "Unhandled Promise Rejection" kya hai aur ye kab hota hai?**
A: Ye tab hota hai jab ek Promise reject hoti hai lekin uske liye koi `.catch()` handler ya `try/catch` (agar `async`/`await` use ho raha hai) exist nahi karta kahi bhi call-chain mein. Iska matlab hai error "silently" application mein reh jaati hai bina properly handle hue — browsers console mein warning dikhate hain, Node.js applications kuch configurations mein **crash** bhi ho sakti hain. Isi liye har async operation ko appropriate error handling ke saath wrap karna production code mein critical practice hai.

## 17. Chapter Summary

`async`/`await` ke saath errors `try/catch` se handle hoti hain — jaisi synchronous code mein hoti hai. Rejected `await`ed Promises automatically thrown errors ban jaati hain. Har async operation ko proper error handling ke saath wrap karna zaroori hai, warna "Unhandled Promise Rejection" application ke liye risky ho sakta hai.

## 18. Revision Checklist

- [ ] `try/catch` async/await ke saath kaise kaam karta hai, clear hai.
- [ ] Granular vs centralized error handling patterns samajh gaye.
- [ ] Unhandled Promise Rejection ka risk yaad hai.

---

**Next:** [03-Sequential-vs-Parallel-Execution.md](./03-Sequential-vs-Parallel-Execution.md)
