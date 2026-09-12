# Memory and Garbage Collection

**Module:** 22-Performance
**Difficulty:** 🔴 Advanced
**Previous:** [01-Big-O-Basics.md](./01-Big-O-Basics.md)
**Next:** [03-Debounce-Throttle-Lazy-Loading.md](./03-Debounce-Throttle-Lazy-Loading.md)

---

## 1. Learning Objectives

- JavaScript ke garbage collection mechanism ka basic concept samajhna.
- Common memory leak patterns identify karna.
- Closures/event-listeners ke memory-implications ko revise karna (Module 08 se aage badhate hue).

## 2. Prerequisites

Module 08 (Scope and Closures, especially chapter 04), Module 15 (Event Loop, especially Heap concept).

## 3. Concept in Simple Hinglish

**Garbage Collection (GC)** JavaScript engine ka automatic process hai jo memory ko free karta hai jab objects ab **use mein nahi** hain — tumhe manually memory "free" karne ki zaroorat nahi (jaise C/C++ mein hoti hai). Lekin ye perfect nahi hai — **memory leaks** tab hote hain jab objects ko accidentally "reachable" rakha jaata hai jab unki actually zaroorat nahi hoti.

## 4. Technical Explanation

**Mark-and-Sweep Algorithm (most common GC approach):**
1. GC "root" objects se start karta hai (global objects, currently-executing function's variables).
2. Har reachable object ko "mark" karta hai (root se traverse karke — direct ya indirect references ke through).
3. Jo objects "unmarked" reh jaate hain (unreachable), unhe "sweep" (delete) kar deta hai memory se.

**Key principle:** Ek object garbage-collected hota hai jab **koi bhi reachable reference** usko point nahi karta — chahe uska original variable `null` ho gaya ho, agar **koi aur** (jaise closure — Module 08, chapter 04) usko abhi bhi reference karta hai, wo zinda rehta hai.

## 5. Syntax

Garbage collection automatic hai — koi direct syntax nahi. Lekin patterns jo isse affect karte hain:
```js
let obj = { data: "large" };
obj = null; // Removes THIS reference — object eligible for GC if no other references exist
```

## 6. Basic Examples

```js
function createObject() {
  let obj = { data: "temporary" };
  return "done";
} // 'obj' goes out of scope here — no longer reachable — eligible for garbage collection

createObject();
```

## 7. Detailed Examples

**Memory leak pattern #1 — forgotten event listeners (connects to Module 12):**
```js
function attachListeners() {
  const largeData = new Array(1000000).fill("data"); // Large data captured in closure

  const button = document.getElementById("myButton");
  button.addEventListener("click", function () {
    console.log(largeData.length); // This closure keeps 'largeData' alive as long as the listener exists!
  });
}

attachListeners();
// Even if 'button' is later removed from the DOM without calling removeEventListener,
// in some scenarios the closure (and 'largeData') can remain in memory — a memory leak.

// ✅ Fix — always clean up listeners when they're no longer needed
function attachListenersFixed() {
  const largeData = new Array(1000000).fill("data");
  const button = document.getElementById("myButton");

  function handleClick() {
    console.log(largeData.length);
  }

  button.addEventListener("click", handleClick);

  // Later, when the button/feature is no longer needed:
  // button.removeEventListener("click", handleClick);
}
```

**Memory leak pattern #2 — uncleared timers (connects to Module 13):**
```js
function startPolling() {
  const largeDataSet = fetchLargeDataSet(); // Assume this returns a big object

  setInterval(() => {
    console.log(largeDataSet.length); // Keeps largeDataSet alive FOREVER, as long as this interval runs
  }, 5000);
}

startPolling();
// If this component/feature is "removed" from the UI but the interval was never cleared,
// it keeps running (and keeping largeDataSet alive) indefinitely!

// ✅ Fix — always store the interval ID and clear it when done
function startPollingFixed() {
  const largeDataSet = fetchLargeDataSet();
  const intervalId = setInterval(() => {
    console.log(largeDataSet.length);
  }, 5000);

  return () => clearInterval(intervalId); // Return a "cleanup function"
}
const stopPolling = startPollingFixed();
// stopPolling(); // Call this when done — releases the interval AND largeDataSet
```

**Memory leak pattern #3 — accidental global variables (connects to Module 01, chapter 05 and Module 08, chapter 01):**
```js
function leakyFunction() {
  accidentalGlobal = "This never gets garbage collected!"; // No let/const/var — becomes global!
}
leakyFunction();
// 'accidentalGlobal' lives on the global object for the ENTIRE lifetime of the application
```

**Using WeakMap to avoid leaks (revisiting Module 10, chapter 04):**
```js
const cache = new WeakMap(); // Keys are weakly held — allows garbage collection

function processElement(element) {
  if (!cache.has(element)) {
    cache.set(element, computeExpensiveResult(element));
  }
  return cache.get(element);
}
// If 'element' (a DOM node, for example) is later removed and has no other references,
// its entry in the WeakMap is automatically cleaned up — no manual cleanup needed!
function computeExpensiveResult(el) { return el; }
```

## 8. Mental Model

Garbage Collector ko socho ek **cleaning staff jo poori building (memory) ko check karta hai** — jo bhi rooms (objects) "kisi se connected" (reachable) nahi hain kisi bhi hallway (reference chain) se front-door (root/global scope) tak, unhe clear kar diya jaata hai. Memory leaks tab hote hain jab tum accidentally ek "invisible thread" (closure, event listener, timer) rakhte ho jo ek room ko front-door se connected rakhta hai, chahe tumhe wo room ab chahiye na ho.

## 9. What Happens Internally?

V8 (aur similar engines) generational garbage collection use karte hain — objects ko "young generation" (naye, frequently collected) aur "old generation" (purane, jo survive kar chuke, less-frequently collected) mein categorize karte hain, performance optimize karne ke liye. Mark-and-sweep (aur variations jaise mark-compact) periodically background mein chalte hain, application execution ko minimally interrupt karte hue.

## 10. Common Mistakes

- Event listeners/timers ko cleanup na karna jab component/feature "remove" ho jaaye.
- Closures mein unnecessarily large data ko capture karna (Module 08, chapter 04 se yaad karo).
- Accidental global variables (bina strict mode ke) — permanently memory occupy karte hain.

## 11. Edge Cases

Detached DOM nodes ek subtle leak-source hain — agar JavaScript code kisi DOM element ka reference रखता hai (variable mein), even after element `document` se remove ho chuka ho, wo element (aur uski entire subtree) memory mein "zinda" reh sakta hai.

## 12. Real-World Usage

Single-page applications (React, Vue) mein memory leaks especially important concern hain, kyunki application kabhi "fully reload" nahi hoti — agar components repeatedly mount/unmount hote hain bina proper cleanup ke (jaise `useEffect` cleanup functions), memory usage samay ke saath continuously badh sakta hai ("memory bloat").

## 13. Comparison With Related Concepts

| Leak Source | Fix |
|--------------|-----|
| Forgotten event listeners | `removeEventListener()` on cleanup |
| Uncleared timers | `clearInterval()`/`clearTimeout()` on cleanup |
| Accidental globals | Use strict mode (Module 01, chapter 05) |
| Large closures | Minimize captured data, use WeakMap where applicable |

## 14. Practice Questions

1. Garbage Collection ka basic principle kya hai (kaunse objects collect hote hain)?
2. Event listeners memory leaks kaise create kar sakte hain?
3. `WeakMap` regular `Map` se memory-leak-prevention mein kaise different hai?

## 15. Challenge

Ek React-jaisi (conceptual, plain JavaScript mein simulate karke) component-pattern likho jo `setInterval` start kare mount hone pe, aur "unmount" simulate karne wale function mein `clearInterval` properly call kare — dikhao ki bina cleanup ke interval "leak" hota reh jaata.

## 16. Interview Questions

**Q1: JavaScript mein memory leak kaise ho sakta hai, jabki garbage collection automatic hai?**
A: Garbage collection sirf un objects ko collect karta hai jo **unreachable** ho chuke hain (koi bhi active reference unhe point nahi kar raha). Memory leaks tab hote hain jab **accidentally reference bana rehta hai** ek object ko, chahe uski actually zaroorat khatam ho chuki ho — jaise ek event listener jo purane, ab-unnecessary data ko closure ke through capture karta hai, ya ek `setInterval` jo kabhi clear nahi kiya gaya. GC ye objects collect nahi kar sakta kyunki wo technically abhi bhi "reachable" hain, chahe application-logic ke hisaab se unki zaroorat na ho.

**Q2: `WeakMap` memory-leak-prevention mein kaise help karta hai regular `Map` ke comparison mein?**
A: Regular `Map` apni keys ko **"strongly"** hold karta hai — matlab agar ek object `Map` mein key ki tarah hai, wo garbage-collected **nahi** ho sakta chahe kahi aur koi reference na ho, jab tak wo explicitly `Map` se delete na ho. `WeakMap` apni keys ko **"weakly"** hold karta hai — agar koi object sirf `WeakMap` mein hi reference ho raha hai (kahi aur nahi), wo **normally garbage-collected ho sakta hai**, aur `WeakMap` ki wo entry automatically clean ho jaati hai. Ye "metadata associate karna kisi object ke saath, bina uski lifecycle ko affect kiye" jaise use-cases ke liye WeakMap ko perfect banata hai (Module 10, chapter 04 se yaad karo).

## 17. Chapter Summary

JavaScript ki automatic garbage collection unreachable objects ko memory se free karti hai (mark-and-sweep algorithm). Memory leaks tab hote hain jab accidental references (forgotten listeners, uncleared timers, accidental globals, large closures) objects ko unnecessarily "reachable" rakhte hain. `WeakMap`/`WeakSet` in issues ko avoid karne mein help karte hain "weak" references ke through.

## 18. Revision Checklist

- [ ] Garbage collection ka "reachability" principle clear hai.
- [ ] Common memory-leak patterns (listeners, timers, globals) yaad hain.
- [ ] WeakMap ka memory-leak-prevention use-case samajh gaya.

---

**Next:** [03-Debounce-Throttle-Lazy-Loading.md](./03-Debounce-Throttle-Lazy-Loading.md)
