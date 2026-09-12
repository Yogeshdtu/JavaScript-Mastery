# DOM Basics

**Module:** 11-DOM-and-Browser
**Difficulty:** 🟡 Intermediate
**Previous:** [10-Modern-JavaScript/07-Private-Fields-and-Other-Features.md](../10-Modern-JavaScript/07-Private-Fields-and-Other-Features.md)
**Next:** [02-Selecting-and-Creating-Elements.md](./02-Selecting-and-Creating-Elements.md)

---

## 1. Learning Objectives

- DOM (Document Object Model) kya hai, samajhna.
- DOM tree structure aur node types samajhna.
- `window` aur `document` objects ka basic role jaanna.

## 2. Prerequisites

Modules 01-10 complete. Basic HTML knowledge helpful hai but mandatory nahi.

## 3. Concept in Simple Hinglish

**DOM** ek "tree-like representation" hai tumhare HTML page ka, jise JavaScript **read aur modify** kar sakti hai. Jab browser HTML load karta hai, wo usse ek tree structure mein convert kar deta hai (memory mein) — JavaScript is tree ke saath interact karke webpage ko dynamically change kar sakti hai.

## 4. Technical Explanation

**DOM (Document Object Model)** ek programming interface hai jo HTML/XML documents ko ek **tree structure** ki tarah represent karta hai, jisme har HTML element ek "node" hai. JavaScript is tree ko traverse, read, aur modify kar sakti hai.

**Key objects:**
- **`window`:** Browser ka global object — poori browser window ko represent karta hai (includes `document`, `location`, `history`, timers, etc.)
- **`document`:** Poori HTML page ko represent karta hai — DOM tree ka entry point.

**Node types:**
- **Element nodes:** HTML tags (`<div>`, `<p>`, etc.)
- **Text nodes:** Element ke andar ka actual text.
- **Attribute nodes:** `id`, `class`, `src` jaisi attributes (modern DOM mein properties ki tarah treat hoti hain).

## 5. Syntax

```js
window.document; // Usually just accessed as 'document'
document.documentElement; // <html> element
document.body;              // <body> element
document.title;              // Page title
```

## 6. Basic Examples

```js
console.log(document.title); // Current page's title
console.log(document.body);   // The <body> element
```

## 7. Detailed Examples

**Visualizing the DOM tree for a simple HTML:**
```html
<html>
  <head><title>My Page</title></head>
  <body>
    <h1>Hello</h1>
    <p>Welcome to my page</p>
  </body>
</html>
```
**Tree structure:**
```
document
└── html
    ├── head
    │   └── title
    │       └── "My Page" (text node)
    └── body
        ├── h1
        │   └── "Hello" (text node)
        └── p
            └── "Welcome to my page" (text node)
```

**`window` vs `document` — a common confusion:**
```js
console.log(window.innerWidth);  // Browser window's width (window-level property)
console.log(document.title);       // Page's title (document-level property)

// document is actually a property OF window!
console.log(window.document === document); // true
```
`window` **poori browser window** ko represent karta hai (including document, but also things like screen size, history, location). `document` sirf **HTML content** ko represent karta hai. `document` technically `window` ka ek property hai.

**Checking DOM readiness (a critical timing concept):**
```js
console.log(document.readyState); // "loading", "interactive", or "complete"

document.addEventListener("DOMContentLoaded", () => {
  console.log("DOM is fully loaded and parsed!");
  // Safe to manipulate DOM elements here
});
```
Agar JavaScript `<head>` mein hai aur DOM elements ko access karne ki koshish kare **before** wo load hue hon, `null` milega. Isi liye `DOMContentLoaded` event ka wait karna (ya script ko body ke end mein rakhna — Module 01, chapter 02 se yaad karo) important hai.

## 8. Mental Model

DOM ko socho ek **family tree diagram** jaisa — `document` sabse upar ka "ancestor" hai, phir `html`, uske "children" `head` aur `body`, aur unke apne children. JavaScript is family tree ko explore (traverse) kar sakti hai, naye members add kar sakti hai, ya existing members ko modify/remove kar sakti hai.

## 9. What Happens Internally?

Jab browser HTML parse karta hai, wo ek in-memory tree structure banata hai (DOM). Ye tree browser ke rendering engine se connected hota hai — jab bhi DOM change hota hai, browser **reflow/repaint** trigger karta hai taaki visible page update ho (Module 22 - Performance mein iska cost detail se cover hoga).

## 10. Common Mistakes

- Script ko `<head>` mein rakh ke DOM elements access karne ki koshish karna before wo exist karein — `null` milega.
- `window` aur `document` ko interchangeably use karna jab unka scope different hai.
- DOM ko "HTML source code" samajhna — actually ye ek **live, in-memory representation** hai jo JavaScript se modify hone pe HTML source se different ho sakta hai (jab tak tum "view page source" na karo, jo original HTML dikhata hai, current DOM state nahi).

## 11. Edge Cases

```js
console.log(document.getElementById("nonexistent")); // null — no error, just null
```

## 12. Real-World Usage

Har interactive webpage (forms, buttons, dynamic content updates, single-page applications) DOM manipulation pe based hai. React/Vue jaisi libraries internally DOM ko efficiently update karne ke liye "Virtual DOM" jaisi techniques use karti hain, lekin underlying concept yahi DOM hai.

## 13. Comparison With Related Concepts

| Object | Represents |
|--------|-------------|
| `window` | The entire browser window/tab |
| `document` | The HTML page content (part of window) |
| DOM | The tree structure representing document's content |

## 14. Practice Questions

1. `window` aur `document` mein kya relation hai?
2. DOM tree mein "text node" kya hota hai?
3. `DOMContentLoaded` event kyun important hai?

## 15. Challenge

Ek simple HTML page banao 3-level deep nesting ke saath, aur uska DOM tree diagram hath se (text format mein, jaisa upar dikhaya) draw karo.

## 16. Interview Questions

**Q1: DOM kya hai?**
A: DOM (Document Object Model) ek programming interface hai jo HTML document ko ek **tree structure** ki tarah represent karta hai, jisme har HTML element, attribute, aur text ek "node" hai. Browser HTML parse karke ye tree memory mein banata hai, aur JavaScript is tree ko access/modify kar sakti hai — DOM hi wo mechanism hai jo JavaScript ko webpage ke saath dynamically interact karne deta hai.

**Q2: `window` aur `document` mein kya difference hai?**
A: `window` browser ka **global object** hai jo poori browser window/tab ko represent karta hai — isme timers (`setTimeout`), navigation (`location`), browser history, aur `document` khud shamil hain. `document` sirf **current webpage ka HTML content** represent karta hai — DOM tree ka root access point. Technically, `document` `window` ka ek property hai (`window.document`).

## 17. Chapter Summary

DOM HTML document ka tree-structured, in-memory representation hai jise JavaScript access/modify kar sakti hai. `window` poori browser window represent karta hai, `document` (jo `window` ka property hai) sirf page content represent karta hai. DOM manipulation se pehle uska "ready" hona ensure karna zaroori hai.

## 18. Revision Checklist

- [ ] DOM tree ka structure (nodes, parent-child) clear hai.
- [ ] `window` vs `document` ka difference yaad hai.
- [ ] `DOMContentLoaded` ki importance samajh gayi.

---

**Next:** [02-Selecting-and-Creating-Elements.md](./02-Selecting-and-Creating-Elements.md)
