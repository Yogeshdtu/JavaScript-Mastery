# Structural Patterns — Adapter, Decorator, Proxy, Composition

**Module:** 25-Design-Patterns
**Difficulty:** 🔴 Advanced
**Previous:** [02-Behavioral-Patterns.md](./02-Behavioral-Patterns.md)
**Next:** [26-Professional-JavaScript/01-Code-Quality-and-Style.md](../26-Professional-JavaScript/01-Code-Quality-and-Style.md)

---

## 1. Learning Objectives

- Adapter pattern se incompatible interfaces ko compatible banana.
- Decorator pattern se functionality ko dynamically extend karna.
- Proxy pattern ko revise karna (Module 24, chapter 04 se connected) design-pattern ke perspective se.
- Composition over Inheritance ka principle samajhna.

## 2. Prerequisites

Module 24, chapter 04 (Proxy/Reflect), Module 10, chapter 02 (Classes/Inheritance).

## 3. Concept in Simple Hinglish

**Structural patterns** batate hain ki objects/classes ko kaise **combine/organize** kiya jaaye larger structures banane ke liye. **Adapter** do incompatible cheezo ko saath kaam karne layak banata hai. **Decorator** functionality ko dynamically "wrap" karke add karta hai. **Composition** (inheritance ka alternative) chhoti, reusable pieces ko jodhkar complex behavior banata hai.

## 4. Technical Explanation

**Adapter Pattern:** Ek "wrapper" jo ek interface ko dusre expected interface mein convert karta hai — jaise ek real-world "plug adapter" jo ek country ke plug ko dusre country ke socket mein fit karta hai.

**Decorator Pattern:** Object ki functionality ko **runtime pe extend** karta hai bina uski class ko modify kiye — multiple decorators combine ho sakte hain.

**Composition over Inheritance:** Ek widely-recommended principle jo suggest karta hai ki complex behavior banane ke liye classes ko deeply extend karne (inheritance) ke bajaye, chhote, focused pieces ko combine (compose) karna better hai.

## 5. Syntax

```js
// Adapter
function adaptOldAPI(oldAPIResponse) {
  return { data: oldAPIResponse.result, status: oldAPIResponse.code };
}

// Decorator
function withLogging(fn) {
  return function (...args) {
    console.log(`Calling with args: ${args}`);
    return fn(...args);
  };
}
```

## 6. Basic Examples

```js
// Adapting a third-party library's response format to your app's expected format
function adaptWeatherAPI(externalResponse) {
  return {
    temperature: externalResponse.temp_celsius,
    condition: externalResponse.weather_description
  };
}

const external = { temp_celsius: 28, weather_description: "Sunny" };
console.log(adaptWeatherAPI(external)); // { temperature: 28, condition: "Sunny" }
```

## 7. Detailed Examples

**Adapter Pattern — integrating an incompatible third-party library:**
```js
// Third-party library has this interface (you can't change it)
class OldPaymentGateway {
  makePayment(amountInCents) {
    console.log(`Processing ${amountInCents} cents via OLD gateway`);
  }
}

// Your application expects THIS interface everywhere
class ModernPaymentInterface {
  pay(amountInRupees) {
    throw new Error("Not implemented");
  }
}

// The Adapter bridges the gap
class PaymentAdapter extends ModernPaymentInterface {
  #oldGateway;
  constructor() {
    super();
    this.#oldGateway = new OldPaymentGateway();
  }
  pay(amountInRupees) {
    const amountInCents = amountInRupees * 100; // Converting units, adapting the interface
    this.#oldGateway.makePayment(amountInCents);
  }
}

function processCheckout(paymentProcessor, amount) {
  paymentProcessor.pay(amount); // Application code only knows about ModernPaymentInterface
}

processCheckout(new PaymentAdapter(), 500); // "Processing 50000 cents via OLD gateway"
```
**Why this matters:** Ye application code ko old-gateway ke specific details (cents, method-naming) se **completely isolate** karta hai — agar future mein gateway change ho, sirf Adapter update hota hai, baaki application untouched rehta hai (similar isolation-benefit jo Factory pattern deta tha, chapter 01).

**Decorator Pattern — dynamically adding behavior (functional style):**
```js
function withLogging(fn) {
  return function (...args) {
    console.log(`Calling ${fn.name} with:`, args);
    const result = fn(...args);
    console.log(`Result:`, result);
    return result;
  };
}

function withTiming(fn) {
  return function (...args) {
    const start = performance.now();
    const result = fn(...args);
    console.log(`Took ${(performance.now() - start).toFixed(2)}ms`);
    return result;
  };
}

function add(a, b) {
  return a + b;
}

// Combining multiple decorators!
const decoratedAdd = withLogging(withTiming(add));
decoratedAdd(3, 4);
// "Calling add with: [3, 4]" (approximately — fn.name may show "anonymous" depending on wrapping)
// "Took 0.05ms"
// "Result: 7"
```
Ye pattern Module 22's memoization (chapter 04) aur Module 21's mocking-wrappers (chapter 02) se conceptually related hai — sab "wrap a function to add behavior" ke variations hain.

**Class-based Decorator — extending an object's capabilities:**
```js
class Coffee {
  cost() { return 100; }
  description() { return "Coffee"; }
}

class MilkDecorator {
  #coffee;
  constructor(coffee) { this.#coffee = coffee; }
  cost() { return this.#coffee.cost() + 20; }
  description() { return this.#coffee.description() + " + Milk"; }
}

class SugarDecorator {
  #coffee;
  constructor(coffee) { this.#coffee = coffee; }
  cost() { return this.#coffee.cost() + 10; }
  description() { return this.#coffee.description() + " + Sugar"; }
}

let order = new Coffee();
order = new MilkDecorator(order);
order = new SugarDecorator(order);

console.log(order.description()); // "Coffee + Milk + Sugar"
console.log(order.cost());          // 130
```

**Composition over Inheritance — the principle in action:**
```js
// ⚠️ Inheritance approach — becomes rigid quickly
class FlyingAnimal { fly() { console.log("Flying"); } }
class SwimmingAnimal { swim() { console.log("Swimming"); } }
// A Duck can both fly AND swim — but JavaScript classes only support single inheritance!
// class Duck extends FlyingAnimal, SwimmingAnimal {} // ❌ Not valid syntax!

// ✅ Composition approach — mix and match capabilities freely
const canFly = (state) => ({ fly: () => console.log(`${state.name} is flying`) });
const canSwim = (state) => ({ fly: () => console.log(`${state.name} is swimming`) }); // (typo intentional avoided below)

function createDuck(name) {
  const state = { name };
  return {
    ...state,
    ...canFly(state),
    swim: () => console.log(`${state.name} is swimming`)
  };
}

const duck = createDuck("Donald");
duck.fly();  // "Donald is flying"
duck.swim(); // "Donald is swimming"
```
Composition **flexibly** capabilities ko mix karne deta hai bina rigid class-hierarchies ke constraints ke — ye widely-cited software-design principle hai ("favor composition over inheritance") jo deep inheritance-chains (jo Module 10's `extends` se create ho sakti hain) ke maintenance-problems ko avoid karta hai.

## 8. Mental Model

Adapter ko socho ek **language translator** — do log jo different languages bolte hain (incompatible interfaces), translator (adapter) beech mein khada hoke communication possible banata hai bina dono logo ko apni language change karne ki zaroorat ke. Decorator ko socho **coffee mein toppings add karna** — base coffee wahi rehta hai, but har topping (decorator) usme kuch add kar deta hai, aur tum jitne chaho utne toppings combine kar sakte ho.

## 9. What Happens Internally?

Ye patterns bhi (Behavioral patterns ki tarah) pure JavaScript-capabilities ka organized use hain — Adapter internally sirf method-calls ko translate/wrap karta hai, Decorator higher-order-functions (Module 05, chapter 04) ya composition ka use karta hai. Koi special engine-level behavior involved nahi — ye "how to organize your code" ke patterns hain, "what the engine does" ke nahi.

## 10. Common Mistakes

- Adapter pattern ko unnecessarily use karna jab interfaces already compatible hain.
- Decorators ko itna deeply nest karna ki debugging/tracing mushkil ho jaaye (kaunsa decorator kya add kar raha hai).
- Deep inheritance-hierarchies (`class A extends B extends C extends D`) banana jab composition zyada flexible, maintainable solution deta.

## 11. Edge Cases

Decorator pattern (class-based version) ko JavaScript's native `@decorator` syntax (ek TC39 proposal, Module 10 chapter 01 ke "ES6+ evolution" se related) se bhi implement kiya ja sakta hai modern tooling ke saath — ye is chapter ke manual-wrapping approach ka syntactic-sugar version hai, though widespread stabilization still evolving hai.

## 12. Real-World Usage

Adapter pattern legacy-system-integration, third-party-API-normalization mein common hai. Decorator pattern middleware (Module 19, chapter 02's Express middleware conceptually related hai), aur higher-order-components (React) mein use hota hai. Composition-over-inheritance modern React (hooks-based, function-composition) aur general JavaScript best-practices mein heavily emphasized hai.

## 13. Comparison With Related Concepts

| Pattern | Purpose |
|---------|---------|
| Adapter | Making incompatible interfaces work together |
| Decorator | Dynamically adding behavior without modifying original |
| Proxy (Module 24, ch 04) | Intercepting/controlling access to an object |
| Composition | Building complex behavior from small, combinable pieces |

## 14. Practice Questions

1. Adapter pattern kis situation mein use hota hai?
2. Multiple decorators ko combine karne ka kya benefit hai?
3. Composition inheritance se zyada flexible kyun mana jaata hai?

## 15. Challenge

Ek `withCache` decorator function likho (Module 22, chapter 04's memoization se related) jo kisi function ko wrap kare aur uske results cache kare, phir isse `withLogging` decorator ke saath combine karo ek hi function pe.

## 16. Interview Questions

**Q1: Adapter pattern kya problem solve karta hai, real-world example ke saath?**
A: Adapter pattern do **incompatible interfaces** ko saath kaam karne layak banata hai bina unme se kisi ko modify kiye — ek "wrapper/bridge" create karke jo ek interface ko dusre expected-format mein translate karta hai. Real-world example: agar tumhari application ek specific payment-interface (`pay(amountInRupees)`) expect karti hai, lekin ek third-party library different interface (`makePayment(amountInCents)`) provide karti hai, ek Adapter class dono ke beech translate kar sakti hai (units convert karna, method-names map karna) — application code third-party library ke specific details se completely isolated reh jaata hai.

**Q2: "Composition over Inheritance" principle ka matlab kya hai, aur ye kyun recommend kiya jaata hai?**
A: Ye principle suggest karta hai ki complex object-behavior banane ke liye, deep class-hierarchies (`extends`, Module 10 chapter 02) create karne ke bajaye, chhote, independent, reusable "pieces" (functions/objects jo specific capabilities provide karte hain) ko **combine (compose)** karna generally better hai. Inheritance rigid hoti hai — JavaScript classes sirf single-inheritance support karti hain, aur deep hierarchies "fragile base class problem" jaisi maintenance-issues create kar sakti hain (parent-class change karne se saare descendants unexpectedly affect ho sakte hain). Composition flexibility deta hai — capabilities ko freely mix-and-match kiya ja sakta hai bina rigid parent-child relationships ke constraints ke, jo code ko zyada maintainable aur adaptable banata hai.

## 17. Chapter Summary

Adapter pattern incompatible interfaces ko compatible banata hai wrapper ke through. Decorator pattern functionality ko dynamically, combinably extend karta hai bina original ko modify kiye. Composition-over-inheritance principle deep class-hierarchies ke bajaye chhote, combinable pieces se complex behavior banane ko recommend karta hai — modern JavaScript development mein widely-embraced approach.

## 18. Revision Checklist

- [ ] Adapter pattern ka real-world use-case (third-party-integration) clear hai.
- [ ] Decorator pattern likh sakta hoon (functional aur class-based dono variants).
- [ ] Composition-over-inheritance ka reasoning samajh gaya.

---

**Module 25 Complete!** Next Module: [26-Professional-JavaScript/01-Code-Quality-and-Style.md](../26-Professional-JavaScript/01-Code-Quality-and-Style.md)
