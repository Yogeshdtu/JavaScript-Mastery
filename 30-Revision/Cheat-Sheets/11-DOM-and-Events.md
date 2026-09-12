# Cheat Sheet: DOM and Events

**Full detail:** [11-DOM-and-Browser/](../../11-DOM-and-Browser/), [12-Events/](../../12-Events/)

---

## Selecting Elements
```js
document.getElementById("id")             // Single, fastest
document.querySelector(".class")           // First match, CSS-selector
document.querySelectorAll(".class")         // Static NodeList
document.getElementsByClassName("class")    // LIVE HTMLCollection
```

## Modifying Content
```js
el.textContent = "text";   // ✅ Safe (plain text always)
el.innerHTML = "<b>x</b>"; // ⚠️ Parses HTML — XSS risk with untrusted input
```

## Classes
```js
el.classList.add("x");
el.classList.remove("x");
el.classList.toggle("x");
el.classList.contains("x");
```

## Creating Elements
```js
const el = document.createElement("div"); // Not in DOM yet
parent.appendChild(el); // Now visible
```

## Events
```js
el.addEventListener("click", handler);      // ✅ Modern, multiple handlers allowed
el.addEventListener("click", handler, true); // Capturing phase
event.stopPropagation();                        // Stop bubbling
```

## Event Delegation (essential pattern)
```js
parent.addEventListener("click", (e) => {
  if (e.target.matches(".child-class")) { /* handle */ }
});
// Works for dynamically-added children too!
```

## Bubbling vs Capturing
Capturing: top-down (document → target). Bubbling: bottom-up (target → document, default).

## Storage
| | Persists | Sent to server? |
|---|---|---|
| `localStorage` | Until cleared | No |
| `sessionStorage` | Tab close | No |
| Cookies | Expiry date | Yes, every request |

---

**Next:** [12-Fetch-and-Modules.md](./12-Fetch-and-Modules.md)
