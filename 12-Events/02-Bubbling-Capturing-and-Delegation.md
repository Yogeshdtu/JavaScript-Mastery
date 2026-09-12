# Event Bubbling, Capturing, and Delegation

**Module:** 12-Events
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [01-Event-Basics-and-Listeners.md](./01-Event-Basics-and-Listeners.md)
**Next:** [13-Asynchronous-JavaScript/01-Sync-vs-Async.md](../13-Asynchronous-JavaScript/01-Sync-vs-Async.md)

---

## 1. Learning Objectives

- Event bubbling aur capturing phases samajhna.
- `stopPropagation()` ka use samajhna.
- Event delegation pattern implement karna (performance optimization).

## 2. Prerequisites

[01-Event-Basics-and-Listeners.md](./01-Event-Basics-and-Listeners.md)

## 3. Concept in Simple Hinglish

Jab tum kisi nested element pe click karte ho (jaise ek button jo `<div>` ke andar hai), event sirf us button tak limited nahi rehta — ye **upar ki taraf "bubble" (bulbula banke uthta)** hai through all its parent elements. "Event delegation" is behavior ka smart use hai — child elements pe individually listeners lagane ke bajaye, ek hi listener parent pe lagana.

## 4. Technical Explanation

**3 Phases of Event Propagation:**
1. **Capturing Phase:** Event `document` se target element ki taraf **neeche** travel karta hai.
2. **Target Phase:** Event apne actual target element pe pahunchta hai.
3. **Bubbling Phase (default):** Event target se wapas **upar** `document` tak travel karta hai.

By default, `addEventListener` **bubbling phase** mein listen karta hai (3rd argument `true` pass karke capturing phase mein listen kiya ja sakta hai).

## 5. Syntax

```js
element.addEventListener("click", handler);           // Bubbling phase (default)
element.addEventListener("click", handler, true);       // Capturing phase
event.stopPropagation();  // Stops further propagation
event.stopImmediatePropagation(); // Stops propagation AND other listeners on same element
```

## 6. Basic Examples

```js
document.getElementById("outer").addEventListener("click", () => console.log("Outer clicked"));
document.getElementById("inner").addEventListener("click", () => console.log("Inner clicked"));

// Clicking the inner element (nested inside outer) logs:
// "Inner clicked"
// "Outer clicked"  <- Bubbled up!
```

## 7. Detailed Examples

**Visualizing bubbling with a 3-level nested structure:**
```html
<div id="grandparent">
  <div id="parent">
    <button id="child">Click Me</button>
  </div>
</div>
```
```js
document.getElementById("grandparent").addEventListener("click", () => console.log("Grandparent"));
document.getElementById("parent").addEventListener("click", () => console.log("Parent"));
document.getElementById("child").addEventListener("click", () => console.log("Child"));

// Clicking the button logs, in order:
// Child
// Parent
// Grandparent
```

**Stopping propagation:**
```js
document.getElementById("child").addEventListener("click", function (event) {
  console.log("Child clicked");
  event.stopPropagation(); // Prevents bubbling to Parent/Grandparent
});
document.getElementById("parent").addEventListener("click", () => console.log("Parent — never runs now"));

// Clicking the button now logs ONLY: "Child clicked"
```

**Event Delegation — the powerful, performance-friendly pattern:**
```js
// ⚠️ Inefficient approach — adding a listener to EVERY list item
const items = document.querySelectorAll(".list-item");
items.forEach(item => {
  item.addEventListener("click", function () {
    console.log("Clicked:", this.textContent);
  });
});
// Problem: if new items are added dynamically LATER, they won't have listeners!

// ✅ Event Delegation — ONE listener on the parent, using bubbling
document.getElementById("list-container").addEventListener("click", function (event) {
  if (event.target.classList.contains("list-item")) {
    console.log("Clicked:", event.target.textContent);
  }
});
// This works even for items added to the list AFTER this listener was set up!
```
**Kyun better hai?** (1) **Performance:** Bahut saare individual listeners lagane ke bajaye ek hi listener kaafi hai. (2) **Dynamic elements:** Naye add kiye gaye elements (jaise dynamically-created list items) **automatically** kaam karte hain, kyunki listener parent pe hai aur bubbling unhe automatically cover kar leti hai — koi naya listener manually add karne ki zaroorat nahi.

**Capturing phase — a rarer but valid use-case:**
```js
document.getElementById("outer").addEventListener("click", () => console.log("Outer (capturing)"), true);
document.getElementById("inner").addEventListener("click", () => console.log("Inner (bubbling)"));

// Clicking inner logs:
// "Outer (capturing)"  <- Fires FIRST during the capturing (downward) phase
// "Inner (bubbling)"
```

## 8. Mental Model

Event bubbling ko socho **ek awaaz jo talab (pond) mein pathar girne se uthti hai** — awaaz center (target) se uthke bahar ki taraf rings mein failti hai (parent elements). Event delegation ko socho ek **building ka reception desk** — har room (child element) mein alag guard rakhne ke bajaye, sirf reception (parent) pe ek guard rakho jo dekh sake kis room se activity aa rahi hai.

## 9. What Happens Internally?

Browser event dispatch algorithm 3 phases follow karta hai (spec-defined: DOM Level 3 Events) — pehle `document` se target tak capturing, phir target pe hi event fire hota hai, phir target se `document` tak bubbling. Har phase mein registered listeners (jo us phase ke liye configured hain) sequentially call hote hain.

## 10. Common Mistakes

- Event bubbling ko "bug" samajhna jab actually ye intended, useful browser behavior hai.
- Har dynamically-added element pe naya listener manually add karna jab event delegation zyada efficient solution hota.
- `stopPropagation()` ko overuse karna, jisse kabhi-kabhi legitimate parent-level functionality (jaise analytics tracking) accidentally break ho jaati hai.

## 11. Edge Cases

Kuch events (jaise `focus`, `blur`) **bubble nahi karte** by default — inke bubble-karne wale versions `focusin`/`focusout` hain. Ye ek common gotcha hai jab log `focus`/`blur` pe delegation try karte hain.

## 12. Real-World Usage

Event delegation to-do lists, dynamic tables, comment sections jaisi UI mein extensively use hota hai jaha items dynamically add/remove hote rehte hain. Modern frameworks (React) internally event delegation use karte hain (ek single listener `root` element pe, saare events wahi se manage hote hain) performance ke liye.

## 13. Comparison With Related Concepts

| Approach | Handles Dynamic Elements? | Performance (many elements) |
|----------|--------------------------------|-----------------------------------|
| Individual listeners per element | ❌ No (unless manually re-added) | ⚠️ Worse with many elements |
| Event delegation (parent listener) | ✅ Yes, automatically | ✅ Better — one listener total |

## 14. Practice Questions

1. Bubbling aur capturing mein kya difference hai (direction ke terms mein)?
2. Event delegation dynamically-added elements ke liye kaise kaam karta hai bina naya listener add kiye?
3. `stopPropagation()` kya karta hai?

## 15. Challenge

Ek `<ul id="taskList">` banao jisme initially 3 `<li>` items hon. Event delegation use karke ek click listener parent pe lagao jo kisi bhi `<li>` pe click hone par usse "completed" class add kare (strikethrough style ke liye). Phir dynamically ek naya `<li>` add karo aur verify karo ki wo bhi bina extra code ke kaam karta hai.

## 16. Interview Questions

**Q1: Event delegation kya hai aur ye useful kyun hai?**
A: Event delegation ek pattern hai jisme hum har child element pe individually event listener lagane ke bajaye, ek **single listener parent element** pe laga dete hain, aur event bubbling ka use karke `event.target` check karke decide karte hain ki actually kaunsa child element clicked hua. Ye do major fayde deta hai: (1) **Performance** — bahut saare individual listeners ke bajaye sirf ek listener chahiye; (2) **Dynamic content support** — baad mein dynamically add kiye gaye elements automatically kaam karte hain bina naya listener attach kiye, kyunki listener hamesha parent pe hi hai jo already exist karta hai.

**Q2: Event bubbling aur capturing mein kya difference hai?**
A: Ye event propagation ke do (out of three) phases hain. **Capturing phase** mein event `document` (ya root) se **target element ki taraf neeche** travel karta hai. **Bubbling phase** mein event target se **wapas upar document tak** travel karta hai — ye default phase hai jisme `addEventListener` listen karta hai (jab tak explicitly `true` third argument pass na ho capturing ke liye). Practical mein bubbling zyada commonly use hoti hai (event delegation ka foundation), capturing rarely explicitly use hoti hai.

## 17. Chapter Summary

Events 3 phases mein propagate hote hain — capturing (top-down), target, bubbling (bottom-up, default listening phase). `stopPropagation()` isse rok deta hai. Event delegation bubbling ka smart use hai — ek parent listener multiple/dynamic children ke events handle kar sakta hai efficiently, without needing individual listeners on each child.

## 18. Revision Checklist

- [ ] Bubbling vs capturing ka direction/order clear hai.
- [ ] Event delegation pattern likh sakta hoon.
- [ ] `stopPropagation()` ka effect samajh gaya.

---

**Module 12 Complete!** Next Module: [13-Asynchronous-JavaScript/01-Sync-vs-Async.md](../13-Asynchronous-JavaScript/01-Sync-vs-Async.md)
