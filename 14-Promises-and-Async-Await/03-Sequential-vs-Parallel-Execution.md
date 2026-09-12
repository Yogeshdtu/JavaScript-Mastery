# Sequential vs Parallel Execution

**Module:** 14-Promises-and-Async-Await
**Difficulty:** 🔴 Advanced
**Previous:** [02-Error-Handling-in-Async-Code.md](./02-Error-Handling-in-Async-Code.md)
**Next:** [15-Event-Loop/01-Call-Stack-Heap-and-WebAPIs.md](../15-Event-Loop/01-Call-Stack-Heap-and-WebAPIs.md)

---

## 1. Learning Objectives

- Sequential `await` calls ka performance-cost samajhna jab operations independent hon.
- `Promise.all()` ke saath `await` combine karke parallel execution achieve karna.
- Kab sequential aur kab parallel execution sahi hai, decide karna.

## 2. Prerequisites

Module 14, chapters 01-02. [13-Asynchronous-JavaScript/04-Promise-Combinators.md](../13-Asynchronous-JavaScript/04-Promise-Combinators.md)

## 3. Concept in Simple Hinglish

Agar tumhare paas 3 independent async operations hain (jo ek dusre pe depend nahi karte), unhe **ek-ek karke `await` karna slow** hai — total time sabka sum ban jaata hai. Unhe **parallel mein chalana** (sabko ek saath start karna) bahut faster hai — total time sabse slow operation jitna hi hota hai.

## 4. Technical Explanation

**Sequential execution:** Har `await` agle operation ko start hone se pehle poora complete hone ka wait karta hai — total time = **sum of all durations**.

**Parallel execution:** Saari async operations turant (bina wait kiye) start ki jaati hain, phir unke results ka wait kiya jaata hai — total time ≈ **duration of the slowest operation**.

## 5. Syntax

```js
// ⚠️ Sequential (slow, if independent)
const a = await taskA();
const b = await taskB();

// ✅ Parallel (fast, for independent tasks)
const [a, b] = await Promise.all([taskA(), taskB()]);
```

## 6. Basic Examples

```js
function delay(value, ms) {
  return new Promise(resolve => setTimeout(() => resolve(value), ms));
}

async function sequential() {
  console.time("sequential");
  const a = await delay("A", 1000);
  const b = await delay("B", 1000);
  console.timeEnd("sequential"); // ~2000ms
  return [a, b];
}

async function parallel() {
  console.time("parallel");
  const [a, b] = await Promise.all([delay("A", 1000), delay("B", 1000)]);
  console.timeEnd("parallel"); // ~1000ms — almost half the time!
  return [a, b];
}
```

## 7. Detailed Examples

**The critical mistake — accidentally serializing independent operations:**
```js
async function getDashboardData() {
  // ⚠️ These 3 API calls DON'T depend on each other, but this code runs them SEQUENTIALLY!
  const userProfile = await fetchUserProfile();   // Takes 300ms
  const notifications = await fetchNotifications(); // Takes 400ms
  const recentActivity = await fetchRecentActivity(); // Takes 500ms
  // Total time: 300 + 400 + 500 = 1200ms
  return { userProfile, notifications, recentActivity };
}
```
**✅ The fix — run independent operations in parallel:**
```js
async function getDashboardDataFast() {
  const [userProfile, notifications, recentActivity] = await Promise.all([
    fetchUserProfile(),      // All three START at the same time!
    fetchNotifications(),
    fetchRecentActivity()
  ]);
  // Total time: max(300, 400, 500) = 500ms — much faster!
  return { userProfile, notifications, recentActivity };
}
```
Ye ek **extremely common real-world performance bug** hai — developers accidentally independent operations ko sequential `await` se likh dete hain jab wo parallel ho sakte the.

**When sequential IS correct — dependent operations:**
```js
// ✅ Correctly sequential — each step NEEDS the previous step's result
async function processOrder(userId) {
  const user = await getUser(userId);          // Need user first...
  const cart = await getCart(user.id);            // ...to get their cart...
  const order = await createOrder(cart.items);    // ...to create the order
  return order;
}
```
Yahan sequential `await` **sahi** hai kyunki har step ko previous step ka result chahiye — inhe parallel nahi kiya ja sakta (ye "dependency chain" hai).

**Mixed pattern — parallel where possible, sequential where necessary:**
```js
async function completeProfile(userId) {
  const user = await getUser(userId); // Must happen first

  // These two only depend on 'user', not on each other — run in parallel!
  const [preferences, activityHistory] = await Promise.all([
    getPreferences(user.id),
    getActivityHistory(user.id)
  ]);

  return { user, preferences, activityHistory };
}
```

**A subtle gotcha — starting promises before awaiting (an alternative parallel pattern):**
```js
async function parallelAlternative() {
  const promiseA = delay("A", 1000); // Starts immediately, NOT awaited yet
  const promiseB = delay("B", 1000); // Also starts immediately

  const a = await promiseA; // Just waiting for something already in progress
  const b = await promiseB;

  return [a, b]; // Total time: ~1000ms — same as Promise.all(), just written differently
}
```
Ye pattern bhi parallel execution achieve karta hai — key insight ye hai ki Promises **create hote hi start ho jaate hain** (unki underlying operation turant shuru hoti hai), `await` sirf **result ka wait** karta hai. `Promise.all()` generally cleaner/more readable hai, but ye samajhna important hai ki asli "parallelism" kaha se aati hai.

## 8. Mental Model

Sequential execution ko socho **ek single chef jo ek-ek dish banata hai, poori tarah khatam karke agli shuru karta hai** — total time sabka sum hai. Parallel execution ko socho **multiple chefs jo apni-apni dish ek saath banate hain** — total time sirf sabse slow dish jitna hai, kyunki sab simultaneously kaam kar rahe hain.

## 9. What Happens Internally?

Jab ek Promise create hoti hai (jaise `fetchData()` call hoti hai), uski underlying operation (jaise network request) **turant Web API ko handoff** ho jaati hai — chahe tum turant `await` karo ya nahi. `Promise.all()` sirf multiple already-started Promises ke results ka **combined wait** create karta hai. Sequential `await` mein, agla operation tab tak **start hi nahi hota** jab tak pehla poora resolve na ho jaaye — yahi extra delay ka source hai.

## 10. Common Mistakes

- Independent async operations ko habitually sequential `await` se likhna (performance loss).
- Dependent operations ko galti se parallel banane ki koshish karna (jab step 2 ko step 1 ka result chahiye) — ye logically incorrect result dega.
- `Promise.all()` mein ek operation fail hone pe poori batch fail ho jaana — agar partial-failure-tolerance chahiye, `Promise.allSettled()` use karo (Module 13, chapter 04).

## 11. Edge Cases

```js
async function example() {
  const results = [];
  for (const url of ["url1", "url2", "url3"]) {
    results.push(await fetch(url)); // ⚠️ Sequential! Each fetch waits for the previous one
  }
  return results;
}
// Better: use Promise.all() with .map() for independent fetches:
// const results = await Promise.all(urls.map(url => fetch(url)));
```
Loop ke andar `await` use karna ek bahut common performance-anti-pattern hai jab operations independent hain.

## 12. Real-World Usage

Dashboard applications (multiple independent widgets ka data), microservices architecture (multiple service calls), aur batch-processing systems mein sequential-vs-parallel decision performance ko significantly affect karta hai — ye ek common code-review/optimization point hai professional teams mein.

## 13. Comparison With Related Concepts

| Scenario | Correct Approach |
|----------|----------------------|
| Operations don't depend on each other | Parallel (`Promise.all()`) |
| Each operation needs the previous one's result | Sequential (`await` one after another) |
| Need all results, some failures OK | `Promise.allSettled()` (parallel, fault-tolerant) |

## 14. Practice Questions

1. 3 independent operations, har ek 500ms leti hai — sequential aur parallel approach mein total time kya hoga?
2. Loop ke andar `await` use karna kab problematic hai?
3. Dependent operations ko parallel karne ki koshish karne se kya issue hoga?

## 15. Challenge

3 mock functions banao (`fetchWeather()`, `fetchNews()`, `fetchStocks()`, har ek ~1 second ki delay ke saath, independent). Pehle inhe sequential `await` se likho aur time measure karo (`console.time`/`console.timeEnd`), phir `Promise.all()` se parallel version likho aur time compare karo.

## 16. Interview Questions

**Q1: Sequential aur parallel async execution mein kya difference hai, aur kab kaunsa use karna chahiye?**
A: Sequential execution mein har `await` agle operation ko start karne se pehle poora complete hone ka wait karta hai — total time sab operations ke duration ka **sum** hota hai. Parallel execution (`Promise.all()` ke saath) mein sabhi operations **ek saath start** hoti hain — total time sirf sabse **slowest** operation jitna hota hai. Sequential tab correct hai jab operations ek dusre pe **dependent** hon (ek ko doosre ka result chahiye). Parallel tab better hai jab operations **independent** hon — significant performance improvement deta hai.

**Q2: Loop ke andar `await` use karna kyun ek common performance mistake hai?**
A: Jab tum `for` loop ke andar `await` use karte ho (jaise `for (const url of urls) { await fetch(url); }`), har iteration **agle wale se pehle poori tarah complete** hoti hai — matlab sab operations sequentially chalti hain, chahe wo ek dusre pe depend na karti ho. Agar 10 independent API calls hon, har ek 200ms le, total time 2000ms ban jaata hai jabki parallel approach (`Promise.all()` + `.map()`) sirf ~200ms mein sab complete kar sakta tha. Ye ek bahut common, easy-to-miss performance bug hai.

## 17. Chapter Summary

Independent async operations ko sequential `await` se likhna unnecessarily slow hota hai — total time sab durations ka sum ban jaata hai. `Promise.all()` (ya pehle sab Promises start karke baad mein await karna) parallel execution achieve karta hai, total time ko sabse slow operation tak reduce karta hai. Dependent operations (jaha ek ka result doosre ko chahiye) ko sequential hi rakhna chahiye.

## 18. Revision Checklist

- [ ] Sequential vs parallel ka time-complexity difference clear hai.
- [ ] Kab independent operations parallel banane hain, identify kar sakta hoon.
- [ ] Loop-ke-andar-await ka performance-anti-pattern yaad hai.

---

**Module 14 Complete!** Next Module: [15-Event-Loop/01-Call-Stack-Heap-and-WebAPIs.md](../15-Event-Loop/01-Call-Stack-Heap-and-WebAPIs.md)
