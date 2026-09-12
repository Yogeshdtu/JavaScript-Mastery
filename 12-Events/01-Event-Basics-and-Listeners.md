# Event Basics and Listeners

**Module:** 12-Events
**Difficulty:** 🟡 Intermediate
**Previous:** [11-DOM-and-Browser/06-Browser-APIs.md](../11-DOM-and-Browser/06-Browser-APIs.md)
**Next:** [02-Bubbling-Capturing-and-Delegation.md](./02-Bubbling-Capturing-and-Delegation.md)

---

## 1. Learning Objectives

- `addEventListener()` se events handle karna.
- Event object ki useful properties samajhna.
- Multiple event handling approaches compare karna.

## 2. Prerequisites

Module 11 complete.

## 3. Concept in Simple Hinglish

**Event** ek "cheez jo hui" hai — user ne click kiya, key press ki, mouse move kiya, form submit kiya. JavaScript inhe "listen" kar sakti hai aur jab wo hon, koi specific code (handler function) run kar sakti hai. Ye webpage ko truly **interactive** banata hai.

## 4. Technical Explanation

**`addEventListener(eventType, handlerFunction, options)`** ek element pe ek event listener attach karta hai. Multiple listeners same event ke liye same element pe add ho sakte hain (unlike older `onclick =` approach jo overwrite kar deta hai).

**Common event types:** `click`, `mouseover`, `mouseout`, `keydown`, `keyup`, `submit`, `change`, `input`, `focus`, `blur`, `load`, `scroll`.

## 5. Syntax

```js
element.addEventListener("eventType", function (event) {
  // handle event
});

element.removeEventListener("eventType", handlerFunction);
```

## 6. Basic Examples

```js
const button = document.getElementById("myButton");

button.addEventListener("click", function () {
  console.log("Button clicked!");
});
```

## 7. Detailed Examples

**Three ways to handle events — and why `addEventListener` wins:**
```js
// ⚠️ Way 1: Inline HTML attribute — avoid! (mixes HTML/JS, hard to maintain, only one handler)
// <button onclick="handleClick()">Click</button>

// ⚠️ Way 2: DOM property assignment — better, but still only ONE handler allowed
button.onclick = function () { console.log("Handler 1"); };
button.onclick = function () { console.log("Handler 2"); }; // Overwrites Handler 1!

// ✅ Way 3: addEventListener — modern, allows MULTIPLE handlers
button.addEventListener("click", () => console.log("Handler A"));
button.addEventListener("click", () => console.log("Handler B"));
// Clicking now logs BOTH "Handler A" and "Handler B"
```

**The Event object — useful properties:**
```js
button.addEventListener("click", function (event) {
  console.log(event.type);        // "click"
  console.log(event.target);       // The actual element that was clicked
  console.log(event.currentTarget); // The element the listener is attached to
  console.log(event.clientX, event.clientY); // Mouse coordinates
});
```

**Keyboard events — accessing key information:**
```js
document.addEventListener("keydown", function (event) {
  console.log(event.key);      // e.g., "Enter", "a", "Shift"
  console.log(event.code);      // e.g., "Enter", "KeyA" (physical key, layout-independent)

  if (event.key === "Enter") {
    console.log("Enter was pressed!");
  }
});
```

**Removing event listeners — requires a named function reference:**
```js
function handleClick() {
  console.log("Clicked!");
}

button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick); // Must be the SAME function reference

// ⚠️ This will NOT work — anonymous functions can't be removed:
button.addEventListener("click", () => console.log("Oops"));
// button.removeEventListener("click", () => console.log("Oops")); // Different function reference — does nothing!
```

**`once` option — auto-removing listener after first trigger:**
```js
button.addEventListener("click", function () {
  console.log("This only runs once!");
}, { once: true });
```

## 8. Mental Model

Event listeners ko socho **security guards jo specific incidents ka wait karte hain** — jaise ek guard "koi bhi darwaza khole" (click event) ka wait karta hai, aur jab wo ho, guard apna predefined action (handler function) perform karta hai. Multiple guards (multiple `addEventListener` calls) same incident pe independently react kar sakte hain.

## 9. What Happens Internally?

Jab event trigger hota hai (jaise click), browser ek **Event object** banata hai jisme us event ki saari details hoti hain, aur registered listeners ko queue mein daal deta hai callback ki tarah call karne ke liye (Module 15 - Event Loop se directly connected — ye macrotask queue ka part hain). Multiple listeners registration-order mein sequentially call hote hain.

## 10. Common Mistakes

- `onclick =` property ko multiple baar set karna aur pehle handler ke overwrite hone se surprised hona.
- Anonymous function ko `addEventListener` mein pass karna aur baad mein `removeEventListener` se remove karne ki koshish karna — fail hoga kyunki reference match nahi karta.
- `event.target` aur `event.currentTarget` ko confuse karna (next chapter mein event delegation ke context mein ye difference critical hai).

## 11. Edge Cases

```js
document.addEventListener("click", () => console.log("Doc listener"));
document.body.addEventListener("click", () => console.log("Body listener"));
// Clicking anywhere in body triggers BOTH — this connects to bubbling (next chapter)
```

## 12. Real-World Usage

Har interactive feature — buttons, forms, dropdowns, modals, drag-and-drop — event listeners pe based hai. Professional codebases hamesha `addEventListener` use karte hain, inline `onclick` attributes almost never (except quick prototypes/demos).

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** `addEventListener()` — multiple handlers, fine-grained control (`once`, `capture` options — next chapter).
**⚠️ Legacy:** Inline `onclick="..."` HTML attributes, `element.onclick = fn` property assignment — purane code mein milenge, single-handler limitation hai.

## 14. Practice Questions

1. `addEventListener` ka `element.onclick = fn` se kya advantage hai?
2. `event.target` kya represent karta hai?
3. Anonymous function listener ko remove karna kyun mushkil hai?

## 15. Challenge

Ek button banao jo click hone pe counter increment kare aur display kare, aur 5 clicks ke baad `{ once: true }` wala ek alag listener trigger ho jo "Milestone reached!" print kare.

## 16. Interview Questions

**Q1: `addEventListener()` inline `onclick` attribute ya `.onclick` property se better kyun hai?**
A: `addEventListener()` ek element pe **multiple independent handlers** register karne deta hai same event ke liye — koi overwrite nahi hota. `.onclick =` (property assignment) aur inline `onclick="..."` attribute dono sirf **ek hi handler** support karte hain — naya assign karne se purana overwrite ho jaata hai. `addEventListener` extra options bhi deta hai (`once`, `capture`, `passive`) jo fine-grained control provide karte hain, aur ye HTML/JS ko cleanly separate rakhta hai (inline attributes maintainability issues create karte hain).

**Q2: `event.target` aur `event.currentTarget` mein kya difference hai?**
A: `event.target` wo **actual element hai jispe event originate hua** (jaise jispe user ne click kiya). `event.currentTarget` wo element hai **jispe currently listener attached hai aur execute ho raha hai**. Agar event bubbling ho rahi hai (next chapter), `target` aur `currentTarget` different ho sakte hain — jaise agar tum `<ul>` pe listener lagao aur user `<li>` pe click kare, `target` = `<li>`, `currentTarget` = `<ul>`.

## 17. Chapter Summary

`addEventListener()` events handle karne ka modern, flexible tareeka hai — multiple handlers allow karta hai, useful options deta hai (`once`, etc.). Event object useful information carry karta hai (`target`, `type`, coordinates, key info). Listener remove karne ke liye named function reference zaroori hai.

## 18. Revision Checklist

- [ ] `addEventListener` ka multi-handler advantage clear hai.
- [ ] Event object ki key properties yaad hain.
- [ ] `event.target` vs `event.currentTarget` ka basic difference samajh gaya.

---

**Next:** [02-Bubbling-Capturing-and-Delegation.md](./02-Bubbling-Capturing-and-Delegation.md)
