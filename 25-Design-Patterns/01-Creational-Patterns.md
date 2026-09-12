# Creational Patterns — Factory, Singleton, Module

**Module:** 25-Design-Patterns
**Difficulty:** 🔴 Advanced
**Previous:** [24-Advanced-JavaScript-Internals/06-Event-Loop-and-Engine-Optimizations.md](../24-Advanced-JavaScript-Internals/06-Event-Loop-and-Engine-Optimizations.md)
**Next:** [02-Behavioral-Patterns.md](./02-Behavioral-Patterns.md)

---

## 1. Learning Objectives

- Factory pattern se flexible object-creation implement karna.
- Singleton pattern ka use-case aur implementation samajhna.
- Module pattern ko revise karna (Module 05, chapter 07 se connected).

## 2. Prerequisites

Module 05 (Functions/Closures), Module 10 (Classes).

## 3. Concept in Simple Hinglish

**Design Patterns** common, recurring problems ke **proven, reusable solutions** hain — ye "code" nahi hain, ye "templates/approaches" hain jo experienced developers ne repeatedly useful paaye hain. **Creational patterns** specifically object-creation ke different, useful tareeke provide karte hain.

## 4. Technical Explanation

**Factory Pattern:** Ek function/method jo object-creation logic ko encapsulate karta hai — caller ko exact class/constructor jaanne ki zaroorat nahi, sirf factory se object "mangwa" leta hai.

**Singleton Pattern:** Ensure karta hai ki ek class ka sirf **ek hi instance** exist kare poori application mein, aur usko globally access karne ka ek point deta hai.

**Module Pattern:** (Module 05, chapter 07 se yaad karo) Encapsulation achieve karta hai closures/IIFE ke through — aaj ES Modules (Module 17) ye naturally provide karte hain.

## 5. Syntax

```js
// Factory
function createShape(type) {
  if (type === "circle") return new Circle();
  if (type === "square") return new Square();
}

// Singleton
class Singleton {
  static #instance;
  static getInstance() {
    if (!Singleton.#instance) Singleton.#instance = new Singleton();
    return Singleton.#instance;
  }
}
```

## 6. Basic Examples

```js
function createUser(type, name) {
  if (type === "admin") {
    return { name, role: "admin", permissions: ["all"] };
  }
  return { name, role: "user", permissions: ["read"] };
}

const admin = createUser("admin", "Rhea");
const regular = createUser("user", "Kiran");
```

## 7. Detailed Examples

**Factory Pattern — a practical example (connects to Module 10's classes):**
```js
class EmailNotification {
  send(message) { console.log(`Email: ${message}`); }
}
class SMSNotification {
  send(message) { console.log(`SMS: ${message}`); }
}
class PushNotification {
  send(message) { console.log(`Push: ${message}`); }
}

function notificationFactory(type) {
  switch (type) {
    case "email": return new EmailNotification();
    case "sms": return new SMSNotification();
    case "push": return new PushNotification();
    default: throw new Error(`Unknown notification type: ${type}`);
  }
}

function notifyUser(type, message) {
  const notifier = notificationFactory(type); // Caller doesn't need to know WHICH class is used
  notifier.send(message);
}

notifyUser("email", "Your order has shipped!"); // "Email: Your order has shipped!"
notifyUser("sms", "Your OTP is 1234");             // "SMS: Your OTP is 1234"
```
**Why this is useful:** Agar kal ek naya notification-type add karna ho (jaise "slack"), sirf `notificationFactory` mein ek naya case add karna padega — `notifyUser` (aur baaki caller-code) ko **bilkul change nahi karna padega**. Ye "Open/Closed Principle" (extension ke liye open, modification ke liye closed) ka practical example hai.

**Singleton Pattern — ensuring a single, shared instance:**
```js
class DatabaseConnection {
  static #instance;
  #connectionId;

  constructor() {
    if (DatabaseConnection.#instance) {
      throw new Error("Use DatabaseConnection.getInstance() instead of 'new'");
    }
    this.#connectionId = Math.random().toString(36).slice(2); // Simulating a unique connection
    console.log(`Creating new connection: ${this.#connectionId}`);
  }

  static getInstance() {
    if (!DatabaseConnection.#instance) {
      DatabaseConnection.#instance = new DatabaseConnection();
    }
    return DatabaseConnection.#instance;
  }

  query(sql) {
    console.log(`[${this.#connectionId}] Executing: ${sql}`);
  }
}

const db1 = DatabaseConnection.getInstance(); // "Creating new connection: abc123"
const db2 = DatabaseConnection.getInstance(); // (nothing logged — reuses existing instance!)

console.log(db1 === db2); // true — SAME instance
db1.query("SELECT * FROM users"); // "[abc123] Executing: ..."
```
**Real-world rationale:** Database connections, configuration-managers, aur logging-services aksar Singleton pattern use karte hain — bahut saare independent connections/instances banana wasteful ya even problematic ho sakta hai (jaise connection-pool limits, Module 20 chapter 04 se yaad karo).

**Module Pattern — revisiting Module 05, chapter 07 with a modern lens:**
```js
// ⚠️ Legacy — IIFE-based module pattern (Module 05, chapter 07)
const CounterModule = (function () {
  let count = 0; // Private via closure
  return {
    increment() { return ++count; },
    reset() { count = 0; }
  };
})();

// ✅ Modern — ES Module (Module 17, chapter 01) achieves the same encapsulation naturally
// counter.js
// let count = 0;
// export function increment() { return ++count; }
// export function reset() { count = 0; }
```

## 8. Mental Model

Factory pattern ko socho ek **restaurant order-counter** — customer sirf "ek pizza chahiye" bolta hai, exact recipe/process nahi jaanta — counter (factory) decide karta hai kaise banaye, kya ingredients use kare. Singleton ko socho **ek company ka CEO** — poori company mein sirf ek hi CEO hota hai, aur jab bhi koi "CEO se baat karo" kehta hai, sabko wahi ek person milta hai, naya CEO nahi banta har baar.

## 9. What Happens Internally?

Factory pattern internally sirf conditional-logic (if/switch) hai jo appropriate constructor/class choose karta hai — koi special engine-behavior nahi, purely ek organizational pattern hai. Singleton private static field (`#instance`, Module 10 chapter 07 se yaad karo) use karke ek single reference ko class ke andar hi maintain karta hai, aur constructor ko directly `new` se call hone se explicitly rokta hai.

## 10. Common Mistakes

- Factory pattern ko overuse karna simple cases mein jaha directly `new ClassName()` kaafi hota — pattern tab useful hai jab creation-logic genuinely complex/conditional ho.
- Singleton pattern ko overuse karna — ye "global state" create karta hai jo testing (Module 21) ko mushkil bana sakta hai agar carefully use na kiya jaaye.
- Module pattern (IIFE) ko naye code mein use karna jab ES Modules (Module 17) zyada modern, cleaner solution hain.

## 11. Edge Cases

Singleton pattern multi-module/multi-file applications mein tricky ho sakta hai agar bundling-configuration galat ho — kabhi-kabhi accidentally "multiple singletons" ban sakte hain agar module multiple baar (different paths se) import ho jaaye kisi build-tool ki misconfiguration ki wajah se.

## 12. Real-World Usage

Factory pattern UI-component-libraries mein (different component-types create karne ke liye), aur API-client-libraries mein (different request-types handle karne ke liye) common hai. Singleton pattern configuration-management, logging-services, aur database-connection-managers mein use hota hai.

## 13. Comparison With Related Concepts

| Pattern | Solves |
|---------|--------|
| Factory | Encapsulating complex/conditional object-creation logic |
| Singleton | Ensuring only one instance exists, globally accessible |
| Module | Encapsulation/privacy (✅ now achieved via ES Modules, Module 17) |

## 14. Practice Questions

1. Factory pattern ka main benefit kya hai naye types add karte waqt?
2. Singleton pattern real-world mein kab appropriate hai?
3. Module pattern (IIFE) ko modern code mein kis se replace kiya jaata hai?

## 15. Challenge

Ek `loggerFactory(type)` function banao jo `"console"` ya `"silent"` type ke liye different logger-objects return kare (`{log(message) {...}}` shape ka), aur inhe test karo.

## 16. Interview Questions

**Q1: Factory pattern kya problem solve karta hai, aur ek real-world example do.**
A: Factory pattern object-creation logic ko **encapsulate** karta hai ek dedicated function/method mein, taaki calling-code ko exact class/constructor-details jaanne ki zaroorat na pade — sirf "mujhe ye type ka object chahiye" bolna kaafi hai. Real-world example: ek `notificationFactory(type)` jo "email", "sms", ya "push" notification-objects return karta hai based on input — agar future mein "slack" notification add karni ho, sirf factory-function update hoti hai, baaki application-code untouched rehta hai. Ye code ko **maintainable aur extensible** banata hai.

**Q2: Singleton pattern kab use karna appropriate hai, aur iske potential downsides kya hain?**
A: Singleton appropriate hai jab genuinely **sirf ek instance** application-wide meaningful ho — jaise database-connection-manager, application-configuration, ya centralized logging-service, jaha multiple independent instances wasteful ya inconsistent-state create kar sakte hain. Downside ye hai ki Singletons effectively **global state** create karte hain, jo unit-testing (Module 21) ko complicate kar sakta hai (tests ek dusre ko affect kar sakte hain shared-state ke through), aur code ko tightly-coupled bana sakta hai us specific singleton-instance se. Isliye Singleton ko judiciously, sirf genuinely-justified cases mein use karna chahiye.

## 17. Chapter Summary

Factory pattern object-creation-logic ko encapsulate karta hai, extensibility deta hai. Singleton pattern ek single, shared instance guarantee karta hai — database-connections/config-managers jaise use-cases ke liye. Module pattern (historically IIFE-based) encapsulation deta hai — modern JavaScript mein ES Modules (Module 17) ye naturally achieve karte hain.

## 18. Revision Checklist

- [ ] Factory pattern likh sakta hoon aur uska extensibility-benefit samajh gaya.
- [ ] Singleton pattern ka implementation aur appropriate-use-cases yaad hain.
- [ ] Module pattern ka legacy (IIFE) vs modern (ES Modules) evolution clear hai.

---

**Next:** [02-Behavioral-Patterns.md](./02-Behavioral-Patterns.md)
