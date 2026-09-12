# Modifying Elements, Attributes, and Styles

**Module:** 11-DOM-and-Browser
**Difficulty:** 🟡 Intermediate
**Previous:** [02-Selecting-and-Creating-Elements.md](./02-Selecting-and-Creating-Elements.md)
**Next:** [04-Forms-and-Validation.md](./04-Forms-and-Validation.md)

---

## 1. Learning Objectives

- Element content (`textContent`, `innerHTML`, `innerText`) modify karna, aur unke differences samajhna.
- Attributes aur classes manage karna.
- Inline styles JavaScript se control karna.

## 2. Prerequisites

Module 11, chapters 01-02.

## 3. Concept in Simple Hinglish

Ek baar element select ho jaaye, hum uska **content**, **attributes** (jaise `src`, `href`), aur **appearance (styles/classes)** JavaScript se change kar sakte hain — yahi DOM manipulation ka core hai jo webpage ko interactive banata hai.

## 4. Technical Explanation

**Content properties:**
- `textContent`: Sirf plain text, HTML tags ko literal text ki tarah treat karta hai (safe).
- `innerHTML`: HTML ko parse karke render karta hai (powerful but XSS risk agar user-input directly diya jaaye — Module 23 mein detail).
- `innerText`: `textContent` jaisa but CSS-aware hai (hidden elements ka text include nahi karta) — thoda slower hai.

**Attribute methods:**
- `getAttribute()`, `setAttribute()`, `removeAttribute()`

**Class management:**
- `classList.add()`, `.remove()`, `.toggle()`, `.contains()`

**Style manipulation:**
- `element.style.propertyName = value` (camelCase CSS properties)

## 5. Syntax

```js
el.textContent = "New text";
el.innerHTML = "<b>Bold text</b>";
el.setAttribute("data-id", "123");
el.classList.add("active");
el.style.color = "blue";
```

## 6. Basic Examples

```js
const heading = document.getElementById("title");
heading.textContent = "Updated Title";
heading.style.color = "red";
heading.classList.add("highlighted");
```

## 7. Detailed Examples

**`textContent` vs `innerHTML` — the critical safety difference:**
```js
const div = document.createElement("div");
const userInput = "<script>alert('XSS Attack!')</script>Hello";

div.textContent = userInput;
console.log(div.innerHTML);
// "&lt;script&gt;alert('XSS Attack!')&lt;/script&gt;Hello" — HTML is ESCAPED, rendered as plain text (SAFE)

div.innerHTML = userInput; // ⚠️ DANGEROUS if userInput comes from an untrusted source!
// The <script> tag would actually be parsed (modern browsers block inline script execution this way,
// but other HTML injection attacks like <img src=x onerror=...> would still work)
```
**✅ Modern / Recommended:** User-generated ya untrusted content ke liye **hamesha `textContent` use karo**. `innerHTML` sirf tab use karo jab content trusted ho ya properly sanitized ho (Module 23 - Security mein XSS detail se cover hoga).

**Working with attributes:**
```js
const img = document.querySelector("img");

console.log(img.getAttribute("src")); // Current src value
img.setAttribute("src", "new-image.jpg");
img.setAttribute("alt", "A description");

console.log(img.hasAttribute("data-loaded")); // false
img.removeAttribute("alt");

// Direct property access also works for standard attributes:
console.log(img.src); // Often gives the FULL resolved URL, unlike getAttribute()
```

**`classList` — the modern way to manage CSS classes:**
```js
const box = document.getElementById("box");

box.classList.add("active", "visible");   // Add multiple classes
box.classList.remove("hidden");            // Remove a class
box.classList.toggle("expanded");          // Add if absent, remove if present
console.log(box.classList.contains("active")); // true
```
**⚠️ Legacy way:** `box.className = "active visible"` — ye poori class string ko **overwrite** kar deta hai, existing classes preserve nahi karta. `classList` methods individual classes ko safely add/remove karte hain bina baaki ko affect kiye.

**Inline styles vs CSS classes — best practice:**
```js
// ⚠️ Direct style manipulation — OK for dynamic, one-off values
element.style.left = `${xPosition}px`;
element.style.opacity = "0.5";

// ✅ Preferred for toggling predefined states — use classes, let CSS handle styling
element.classList.add("is-hidden"); // CSS: .is-hidden { display: none; }
```
Best practice hai **CSS classes** use karna predefined states (visible/hidden/active) ke liye, aur inline styles sirf tab jab value **dynamically calculated** ho (jaise animation position) — ye styling logic ko CSS mein centralize karta hai, JavaScript mein nahi.

## 8. Mental Model

`textContent` ko socho ek **"plain text typewriter"** — jo bhi likho, wahi literally text ban jaata hai. `innerHTML` ek **"HTML interpreter"** hai — jo likho usse HTML ki tarah parse karta hai, isliye careful rehna padta hai untrusted content ke saath. `classList` ko socho ek **light switch panel** — har class ek switch hai jo independently on/off ho sakta hai.

## 9. What Happens Internally?

Jab `innerHTML` set hota hai, browser us string ko **HTML parser** se re-parse karta hai aur naye DOM nodes banata hai — ye `textContent` se **zyada expensive** operation hai (Module 22 mein performance detail). `classList` operations directly element ke `class` attribute ko underlying manage karte hain, but individual class-level granularity ke saath.

## 10. Common Mistakes

- User input ko directly `innerHTML` mein daal dena bina sanitization ke — XSS vulnerability (Module 23 mein detail).
- `className = "newClass"` use karke accidentally existing classes overwrite kar dena.
- `getAttribute("src")` aur `.src` property ke behavior mein difference na jaanna (property resolved/absolute URL de sakti hai, attribute raw value deta hai).

## 11. Edge Cases

```js
const div = document.createElement("div");
div.innerHTML = "<p>Text</p><p>More</p>";
console.log(div.children.length); // 2 — innerHTML created actual DOM elements
```

## 12. Real-World Usage

`classList` toggling UI states (modals, dropdowns, active tabs) mein extremely common hai. `textContent` safe user-generated content display ke liye standard hai (comments, chat messages, usernames).

## 13. Comparison With Related Concepts

| Property/Method | Use Case | Security Risk |
|-------------------|----------|-------------------|
| `textContent` | Plain text (safe for untrusted content) | None |
| `innerHTML` | Rendering actual HTML (trusted content only) | XSS if used with untrusted input |
| `classList` | Toggling predefined visual states | None |
| `style.property` | Dynamic, calculated values | None |

## 14. Practice Questions

1. `textContent` aur `innerHTML` mein security-related difference kya hai?
2. `classList.toggle()` kya karta hai?
3. `className = "x"` aur `classList.add("x")` mein kya difference hai?

## 15. Challenge

Ek button banao jo click hone pe (abhi bina event listener seekhe, sirf manually function call karke test karo) ek `<div>` ki class `"active"` ko toggle kare, aur agar active hai to uska background color JavaScript se `style` property se set karo.

## 16. Interview Questions

**Q1: `textContent` aur `innerHTML` mein security ke nazariye se kya difference hai?**
A: `textContent` diye gaye string ko **hamesha plain text** ki tarah treat karta hai — chahe usme HTML tags ho, wo literal characters ki tarah escape/display hoti hain, execute nahi hoti. `innerHTML` string ko **actual HTML ki tarah parse** karta hai — agar ye string kisi untrusted source (jaise user input) se aa rahi hai bina sanitization ke, ye XSS (Cross-Site Scripting) attacks ka rasta khol deta hai, jaha malicious scripts/HTML page mein inject ho sakte hain. Isi liye untrusted content ke liye `textContent` (ya proper sanitization ke saath `innerHTML`) use karna best practice hai.

**Q2: `classList` methods `className` property se better kyun hain?**
A: `className = "newClasses"` poori class-string ko **overwrite** kar deta hai — agar tumhe sirf ek class add/remove karni thi, tumhe manually poori string ko parse/rebuild karna padta. `classList.add()`, `.remove()`, `.toggle()` methods **individual classes** ko independently manage karte hain bina baaki existing classes ko touch kiye — ye zyada safe, readable, aur maintainable approach hai.

## 17. Chapter Summary

`textContent` safe plain-text manipulation ke liye hai, `innerHTML` HTML rendering ke liye but security-risky agar untrusted input ke saath use kiya jaaye. Attributes `getAttribute`/`setAttribute` se manage hote hain. `classList` (modern, recommended) individual classes ko `className` (legacy, overwrite-prone) se better manage karta hai.

## 18. Revision Checklist

- [ ] `textContent` vs `innerHTML` ka security-difference clear hai.
- [ ] `classList` methods ka use aata hai.
- [ ] Kab inline styles aur kab CSS classes use karni hain, decide kar sakta hoon.

---

**Next:** [04-Forms-and-Validation.md](./04-Forms-and-Validation.md)
