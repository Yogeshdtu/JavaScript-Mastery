# Behavioral Patterns — Observer, Pub/Sub, Strategy, Command, State

**Module:** 25-Design-Patterns
**Difficulty:** 🔴 Advanced
**Previous:** [01-Creational-Patterns.md](./01-Creational-Patterns.md)
**Next:** [03-Structural-Patterns.md](./03-Structural-Patterns.md)

---

## 1. Learning Objectives

- Observer/Pub-Sub pattern implement karna (Module 18, chapter 04's EventEmitter se connected).
- Strategy pattern se interchangeable algorithms manage karna.
- Command aur State patterns ka basic use-case samajhna.

## 2. Prerequisites

Module 18, chapter 04 (EventEmitter), Module 05 (Higher-Order Functions).

## 3. Concept in Simple Hinglish

**Behavioral patterns** objects ke beech **communication aur responsibility-assignment** ke common patterns hain. **Observer/Pub-Sub** ek "broadcast" system hai (Module 12/18 ke events se directly related). **Strategy** algorithms ko interchangeable banata hai. **Command** actions ko objects mein encapsulate karta hai. **State** object-behavior ko uski current "state" ke hisaab se badalta hai.

## 4. Technical Explanation

**Observer Pattern:** Ek "subject" object apni state-changes ke baare mein multiple "observers" ko notify karta hai.

**Pub/Sub Pattern:** Observer se similar, but ek "event bus/broker" ke through indirect — publishers aur subscribers ek dusre ko directly nahi jaante (Observer mein subject directly observers ko track karta hai).

**Strategy Pattern:** Different algorithms ko interchangeable "strategy" objects/functions ki tarah encapsulate karna, runtime pe switch kiya ja sake.

## 5. Syntax

```js
// Observer/Pub-Sub (revisiting Module 18, chapter 04)
const emitter = new EventEmitter();
emitter.on("event", callback);
emitter.emit("event", data);

// Strategy
function processPayment(strategy, amount) {
  return strategy(amount);
}
```

## 6. Basic Examples

```js
const creditCardStrategy = (amount) => `Paid ₹${amount} via Credit Card`;
const upiStrategy = (amount) => `Paid ₹${amount} via UPI`;

function checkout(paymentStrategy, amount) {
  return paymentStrategy(amount);
}

console.log(checkout(creditCardStrategy, 500)); // "Paid ₹500 via Credit Card"
console.log(checkout(upiStrategy, 500));           // "Paid ₹500 via UPI"
```

## 7. Detailed Examples

**Observer Pattern — from scratch (formalizing what EventEmitter, Module 18 ch 04, does internally):**
```js
class Subject {
  #observers = [];

  subscribe(observer) {
    this.#observers.push(observer);
  }

  unsubscribe(observer) {
    this.#observers = this.#observers.filter(obs => obs !== observer);
  }

  notify(data) {
    this.#observers.forEach(observer => observer(data));
  }
}

const stockPrice = new Subject();

function investorA(price) { console.log(`Investor A sees price: ${price}`); }
function investorB(price) { console.log(`Investor B sees price: ${price}`); }

stockPrice.subscribe(investorA);
stockPrice.subscribe(investorB);

stockPrice.notify(150); // Both investors are notified
// "Investor A sees price: 150"
// "Investor B sees price: 150"
```

**Pub/Sub — decoupled via a central event bus (a step further than direct Observer):**
```js
class EventBus {
  #events = {};

  subscribe(eventName, callback) {
    if (!this.#events[eventName]) this.#events[eventName] = [];
    this.#events[eventName].push(callback);
  }

  publish(eventName, data) {
    (this.#events[eventName] || []).forEach(callback => callback(data));
  }
}

const bus = new EventBus();

// Different, UNRELATED parts of an application can communicate without direct references!
bus.subscribe("userLoggedIn", (user) => console.log(`Analytics: tracking login for ${user.name}`));
bus.subscribe("userLoggedIn", (user) => console.log(`UI: updating welcome message for ${user.name}`));

bus.publish("userLoggedIn", { name: "Tanya" });
// "Analytics: tracking login for Tanya"
// "UI: updating welcome message for Tanya"
```
**Key difference from Observer:** Publishers (jo `publish()` call karte hain) aur subscribers ek dusre ko **directly nahi jaante** — sab kuch central `EventBus` ke through hota hai, jo components ko highly **decoupled** rakhta hai.

**Strategy Pattern — swappable algorithms (connects to Module 06's array-sorting concepts):**
```js
const sortStrategies = {
  ascending: (a, b) => a - b,
  descending: (a, b) => b - a,
  byAbsoluteValue: (a, b) => Math.abs(a) - Math.abs(b)
};

function sortNumbers(numbers, strategyName) {
  return [...numbers].sort(sortStrategies[strategyName]); // Module 06, chapter 02's sort()!
}

const nums = [5, -3, 1, -8, 2];
console.log(sortNumbers(nums, "ascending"));       // [-8, -3, 1, 2, 5]
console.log(sortNumbers(nums, "byAbsoluteValue"));  // [1, 2, -3, 5, -8]
```
**Why this is useful:** Naya sorting-strategy add karna sirf `sortStrategies` object mein ek naya entry add karne jaisa simple hai — `sortNumbers` function ko modify karne ki zaroorat nahi (Factory pattern ke similar extensibility-benefit, previous chapter).

**Command Pattern — encapsulating actions as objects (useful for undo/redo):**
```js
class AddTextCommand {
  constructor(document, text) {
    this.document = document;
    this.text = text;
  }
  execute() {
    this.document.content += this.text;
  }
  undo() {
    this.document.content = this.document.content.slice(0, -this.text.length);
  }
}

const doc = { content: "" };
const commandHistory = [];

function executeCommand(command) {
  command.execute();
  commandHistory.push(command);
}

function undoLastCommand() {
  const command = commandHistory.pop();
  if (command) command.undo();
}

executeCommand(new AddTextCommand(doc, "Hello "));
executeCommand(new AddTextCommand(doc, "World"));
console.log(doc.content); // "Hello World"

undoLastCommand();
console.log(doc.content); // "Hello "
```

**State Pattern — behavior changes based on internal state:**
```js
class TrafficLight {
  #states = ["red", "green", "yellow"];
  #currentIndex = 0;

  get currentState() {
    return this.#states[this.#currentIndex];
  }

  next() {
    this.#currentIndex = (this.#currentIndex + 1) % this.#states.length;
  }
}

const light = new TrafficLight();
console.log(light.currentState); // "red"
light.next();
console.log(light.currentState); // "green"
light.next();
console.log(light.currentState); // "yellow"
```

## 8. Mental Model

Observer ko socho **ek YouTube channel** — subscribers direct-relationship rakhte hain channel se, jab video (event) aati hai, sab notify hote hain. Pub/Sub ko socho **ek newspaper distribution system** — publisher newspaper ko ek central hub (distributor) ko deta hai, subscribers apne aap hub se receive karte hain, publisher ko individually kaun subscriber hai pata hi nahi hota. Strategy pattern ko socho **different navigation-routes ke options** — same destination (task), different paths (algorithms), aasani se switch ho sakte hain.

## 9. What Happens Internally?

Ye patterns "pure JavaScript" hain — koi special engine-behavior involved nahi. In sabka core mechanism **functions ko values ki tarah store/pass karna** hai (Module 05, chapter 01's "first-class functions") aur **arrays/objects mein callbacks collect karna** (jaise Observer's `#observers` array) — ye patterns essentially JavaScript ki fundamental capabilities ka organized, reusable application hain.

## 10. Common Mistakes

- Observer pattern mein `unsubscribe` ki zaroorat bhool jaana — memory leaks create kar sakta hai (Module 22, chapter 02 se yaad karo).
- Strategy pattern ko overuse karna jab simple `if/else` kaafi ho — pattern tab valuable hai jab genuinely multiple, interchangeable algorithms exist karte hon.
- Pub/Sub ko itna decoupled bana dena ki debugging mushkil ho jaaye ("kaha se event trigger hui, kaha handle ho rahi hai" trace karna hard ho jaaye bahut bade systems mein).

## 11. Edge Cases

Observer pattern mein agar ek observer khud `notify()` ke andar `subscribe`/`unsubscribe` call kare, iteration ke dauraan array-modification issues aa sakte hain — careful implementation (jaise array ko copy karke iterate karna) zaroori hai.

## 12. Real-World Usage

Observer/Pub-Sub DOM events (Module 12), Node.js EventEmitter (Module 18), aur state-management-libraries (Redux, jo essentially ek sophisticated Pub/Sub system hai) mein foundational hai. Strategy pattern payment-processing, sorting/filtering-options, aur validation-rules mein common hai. Command pattern undo/redo-functionality (text-editors, drawing-apps) mein classic use-case hai.

## 13. Comparison With Related Concepts

| Pattern | Coupling | Use Case |
|---------|-------------|--------------|
| Observer | Direct (subject knows observers) | Simple event-notification |
| Pub/Sub | Indirect (via central bus) | Decoupled, cross-component communication |
| Strategy | N/A | Interchangeable algorithms |
| Command | N/A | Encapsulated, undoable actions |

## 14. Practice Questions

1. Observer aur Pub/Sub mein coupling ka difference kya hai?
2. Strategy pattern ka main extensibility-benefit kya hai?
3. Command pattern undo/redo functionality ke liye kyun suited hai?

## 15. Challenge

Ek `EventBus` (upar diye pattern jaisa) use karke ek simple "shopping cart" system banao jaha `addToCart` event publish ho, aur 2 independent subscribers ho — ek jo cart-total update kare, ek jo "item added" notification log kare.

## 16. Interview Questions

**Q1: Observer pattern aur Pub/Sub pattern mein kya difference hai?**
A: Observer pattern mein, "subject" (jo state-changes emit karta hai) **directly** apni observers-list maintain karta hai aur unhe directly notify karta hai — subject aur observers ek dusre ko "jaante" hain. Pub/Sub pattern ek extra layer of indirection add karta hai — ek central **event bus/broker** ke through publishers aur subscribers communicate karte hain, bina ek dusre ko directly reference kiye. Isse Pub/Sub **zyada decoupled** hota hai — components ek dusre ki existence ke baare mein bilkul bhi jaane bina communicate kar sakte hain, jo large-scale applications mein flexibility deta hai (though thoda debugging-complexity ke trade-off ke saath).

**Q2: Strategy pattern kya problem solve karta hai?**
A: Strategy pattern **interchangeable algorithms/behaviors** ko encapsulate karta hai taaki wo runtime pe easily swap ki ja sakein bina calling-code ko change kiye. Ye tab useful hai jab ek task ko multiple different tareeko se accomplish kiya ja sakta ho (jaise different sorting-orders, different payment-methods, different validation-rules) — har approach ko ek separate "strategy" (function/object) ki tarah define karke, application flexible ban jaati hai naya strategy add karne ke liye, without modifying existing, working code (extensibility, similar to the Factory pattern's benefit from the previous chapter).

## 17. Chapter Summary

Observer pattern direct subject-observer notification deta hai. Pub/Sub isko ek central event-bus ke through further decouple karta hai. Strategy pattern algorithms ko interchangeable banata hai extensibility ke liye. Command pattern actions ko objects mein encapsulate karta hai (undo/redo jaise features ke liye). State pattern object-behavior ko current-state ke hisaab se adapt karta hai.

## 18. Revision Checklist

- [ ] Observer vs Pub/Sub ka coupling-difference clear hai.
- [ ] Strategy pattern likh sakta hoon aur uska extensibility-benefit samajh gaya.
- [ ] Command pattern ka undo/redo use-case yaad hai.

---

**Next:** [03-Structural-Patterns.md](./03-Structural-Patterns.md)
