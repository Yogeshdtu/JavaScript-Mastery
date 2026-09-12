# Proxy and Reflect

**Module:** 24-Advanced-JavaScript-Internals
**Difficulty:** 🔴 Expert
**Previous:** [03-Iterators-Generators-Symbols-Deep-Dive.md](./03-Iterators-Generators-Symbols-Deep-Dive.md)
**Next:** [05-WeakRefs-and-Garbage-Collection-Concepts.md](./05-WeakRefs-and-Garbage-Collection-Concepts.md)

---

## 1. Learning Objectives

- `Proxy` se object-operations ko intercept karna.
- `Reflect` ka `Proxy` ke saath complementary use samajhna.
- Real-world meta-programming use-cases (validation, logging, reactive-systems) dekhna.

## 2. Prerequisites

Module 24, chapter 02 (Internal Slots — especially `[[Get]]`/`[[Set]]`).

## 3. Concept in Simple Hinglish

**Proxy** ek "wrapper" object hai jo tumhe kisi doosre object ke **fundamental operations** (property-read, property-write, delete, etc.) ko **intercept aur customize** karne deta hai — jaise ek object ke "middle-man" jo har operation ko customize kar sakta hai. **Reflect** Proxy ka natural companion hai — default behaviors ko explicitly call karne ka standardized tareeka.

## 4. Technical Explanation

`new Proxy(target, handler)` — `target` original object hai, `handler` ek object hai jisme **"traps"** (functions) define hoti hain jo specific operations ko intercept karti hain (jaise `get`, `set`, `has`, `deleteProperty`).

Ye directly Module 24, chapter 02 ke internal methods (`[[Get]]`, `[[Set]]`) se connected hai — Proxy traps essentially in internal methods ko JavaScript-level pe "hook" karne deti hain.

## 5. Syntax

```js
const proxy = new Proxy(target, {
  get(target, property, receiver) { /* custom logic */ return Reflect.get(...arguments); },
  set(target, property, value, receiver) { /* custom logic */ return Reflect.set(...arguments); }
});
```

## 6. Basic Examples

```js
const target = { message: "Hello" };

const proxy = new Proxy(target, {
  get(target, property) {
    console.log(`Property "${property}" was accessed`);
    return target[property];
  }
});

console.log(proxy.message);
// "Property "message" was accessed"
// "Hello"
```

## 7. Detailed Examples

**Validation trap — enforcing rules on property assignment:**
```js
const validator = {
  set(target, property, value) {
    if (property === "age") {
      if (typeof value !== "number" || value < 0) {
        throw new TypeError("Age must be a non-negative number");
      }
    }
    target[property] = value;
    return true; // Must return true to indicate success
  }
};

const person = new Proxy({}, validator);
person.age = 25; // Works fine
console.log(person.age); // 25

// person.age = -5; // ❌ Throws: TypeError: Age must be a non-negative number
// person.age = "old"; // ❌ Throws: TypeError: Age must be a non-negative number
```

**Logging/auditing trap — tracking all property access (connects to Module 21's debugging concepts):**
```js
function createLoggedObject(target) {
  return new Proxy(target, {
    get(target, property, receiver) {
      console.log(`GET: ${String(property)}`);
      return Reflect.get(target, property, receiver); // Delegates to default behavior
    },
    set(target, property, value, receiver) {
      console.log(`SET: ${String(property)} = ${value}`);
      return Reflect.set(target, property, value, receiver);
    }
  });
}

const user = createLoggedObject({ name: "Kavya" });
user.name;          // "GET: name"
user.age = 30;        // "SET: age = 30"
```
**Why use `Reflect` here instead of directly manipulating `target`?** `Reflect.get`/`Reflect.set` ensure karte hain ki default behavior **exactly correctly** replicate ho (including edge cases jaise inherited getters/setters, `receiver` handling) — manually `target[property] = value` likhna most cases mein kaam karega, but `Reflect` methods spec-compliant, more robust tareeka hain.

**Default value trap — a practical utility:**
```js
function withDefaults(target, defaultValue) {
  return new Proxy(target, {
    get(target, property) {
      return property in target ? target[property] : defaultValue;
    }
  });
}

const settings = withDefaults({ theme: "dark" }, "not set");
console.log(settings.theme);     // "dark"
console.log(settings.fontSize);   // "not set" — property doesn't exist, but no error/undefined!
```

**Preventing property deletion — a protective trap:**
```js
const protectedObj = new Proxy(
  { critical: "important data" },
  {
    deleteProperty(target, property) {
      console.log(`Attempted to delete: ${property}`);
      return false; // Deletion silently prevented
    }
  }
);

delete protectedObj.critical; // "Attempted to delete: critical"
console.log(protectedObj.critical); // "important data" — still there!
```

**Reactive systems — the conceptual foundation of frameworks like Vue.js:**
```js
function reactive(obj, onChange) {
  return new Proxy(obj, {
    set(target, property, value, receiver) {
      const result = Reflect.set(target, property, value, receiver);
      onChange(property, value); // Trigger a callback whenever ANY property changes
      return result;
    }
  });
}

const state = reactive({ count: 0 }, (prop, value) => {
  console.log(`State changed: ${prop} is now ${value}`);
  // In a real framework, this would trigger a UI re-render!
});

state.count = 1; // "State changed: count is now 1"
state.count = 2; // "State changed: count is now 2"
```
Ye pattern conceptually wahi hai jo Vue.js jaisi frameworks apne reactivity-system ke liye internally use karti hain — jab data change hoti hai, UI automatically update hoti hai, aur `Proxy` hi wo mechanism hai jo "data change hui" ko detect karta hai.

## 8. Mental Model

Proxy ko socho ek **security-guard/receptionist jo har door (property access) pe khada hai** — koi bhi is doorway se guzarne (property access karne) ke liye, receptionist se guzarna padta hai, jo custom rules apply kar sakta hai (log karna, verify karna, block karna) before actually andar jaane deta hai. `Reflect` ko socho **"standard, default procedure manual"** jise receptionist follow kar sakta hai jab wo khud koi custom decision nahi lena chahta, sirf normal flow continue karna chahta hai.

## 9. What Happens Internally?

Jab tum ek Proxy object pe koi operation (`get`, `set`, `delete`, etc.) perform karte ho, engine **directly target object ke internal methods (`[[Get]]`, `[[Set]]`, Module 24 chapter 02) ko call nahi karta** — instead, ye Proxy ke corresponding "trap" function ko call karta hai (agar defined hai). Trap function decide karta hai kya karna hai — custom logic run karna, `Reflect` ke through default-behavior ko explicitly invoke karna, ya operation ko completely block/modify karna.

## 10. Common Mistakes

- Proxy traps ke andar `Reflect` use na karke directly `target[property] = value` likhna — most cases mein kaam karta hai, but edge cases (inherited accessors) miss ho sakte hain.
- `set` trap mein `true` return karna bhool jaana — strict mode mein ye `TypeError` cause kar sakta hai (indicates operation "failed").
- Bahut zyada Proxies nest karna, jo performance-overhead aur debugging-complexity add karta hai.

## 11. Edge Cases

Proxy `===` comparison ko intercept **nahi** kar sakta — `proxy === target` hamesha `false` hoga (different object references, jaise Module 02 chapter 05 ka reference-equality concept), chahe proxy target ke behavior ko poori tarah replicate kar raha ho.

## 12. Real-World Usage

Proxy/Reflect modern reactive-frameworks (Vue 3's reactivity system) ke core mein hain. Ye validation-libraries, ORMs (jo database-query-building ke liye elegant syntax provide karte hain — Module 20, chapter 03), aur API-mocking-tools mein bhi use hote hain.

## 13. Comparison With Related Concepts

| Concept | Purpose |
|---------|---------|
| Proxy | Intercepting/customizing fundamental object operations |
| Reflect | Explicitly invoking default behavior for those same operations |
| Getters/Setters (Module 07, ch 04) | Customizing ONE specific property's behavior |
| Proxy traps | Customizing behavior for ALL/ANY property, dynamically |

## 14. Practice Questions

1. Proxy `handler` object mein "trap" ka matlab kya hai?
2. `Reflect.get()`/`Reflect.set()` ko Proxy traps ke andar use karna kyun recommended hai?
3. Proxy `===` comparisons ko intercept kar sakta hai ya nahi?

## 15. Challenge

Ek "read-only" Proxy banao jo kisi bhi object ko wrap kare aur `set`/`deleteProperty` traps ke through saari modifications ko silently prevent kare (ya console warning ke saath), effectively ek "poor man's `Object.freeze()`" jo custom logging bhi de sake.

## 16. Interview Questions

**Q1: `Proxy` kya hai aur ye kis type ke problems solve karta hai?**
A: `Proxy` ek object hai jo kisi target-object ke **fundamental operations** (property read/write/delete, function-calls, etc.) ko **intercept** karne deta hai custom "trap" functions ke through. Ye validation (property-assignment ke time rules enforce karna), logging/auditing (kaunsi properties access ho rahi hain track karna), default-values provide karna, aur **reactive systems** (jaise Vue.js ki reactivity, jaha data-changes automatically UI-updates trigger karte hain) implement karne ke liye use hota hai. Ye JavaScript ki meta-programming capabilities ka ek powerful, modern tool hai.

**Q2: Proxy traps ke andar `Reflect` methods use karna kyun best practice mana jaata hai?**
A: Jab ek Proxy trap (jaise `get`) custom logic ke baad "normal" behavior continue karna chahta hai, `Reflect.get(target, property, receiver)` use karna recommended hai directly `target[property]` access karne ke bajaye — kyunki `Reflect` methods **exactly** wahi default internal-algorithm (`[[Get]]`, Module 24 chapter 02) replicate karte hain jo engine khud use karta, correctly `receiver` parameter ko handle karte hue (jo inheritance-chains mein `this`-binding ke liye important hai, especially jab prototype pe getters/setters involved hon). Manual property-access most simple cases mein kaam kar sakta hai, but `Reflect` zyada robust aur spec-compliant approach hai.

## 17. Chapter Summary

`Proxy` object-operations (get, set, delete, etc.) ko intercept karne ka mechanism deta hai custom "trap" functions ke through — validation, logging, default-values, aur reactive-systems ke liye powerful. `Reflect` in same operations ke default-behavior ko explicitly, correctly invoke karne ka standardized companion-API hai, jo Proxy traps ke andar use karne ke liye best practice hai.

## 18. Revision Checklist

- [ ] Proxy trap-mechanism (`get`, `set`, `deleteProperty`) ka syntax aur use-case clear hai.
- [ ] `Reflect` ko Proxy traps ke andar use karne ka reason samajh gaya.
- [ ] Reactive-system pattern (Proxy `set` trap + callback) ka basic idea yaad hai.

---

**Next:** [05-WeakRefs-and-Garbage-Collection-Concepts.md](./05-WeakRefs-and-Garbage-Collection-Concepts.md)
