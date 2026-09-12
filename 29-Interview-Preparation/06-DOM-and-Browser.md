# Interview Prep: DOM and Browser

**Module:** 29-Interview-Preparation
**Previous:** [05-Async-JavaScript.md](./05-Async-JavaScript.md)
**Next:** [07-NodeJS.md](./07-NodeJS.md)

---

**Q1: `window` aur `document` mein kya difference hai?**
A: `window` poori browser-window represent karta hai (timers, location, history sab shamil hain). `document` sirf HTML-content represent karta hai — `document` technically `window` ka property hai. (Detail: [11-DOM-and-Browser/01-DOM-Basics.md](../11-DOM-and-Browser/01-DOM-Basics.md))

**Q2: `textContent` aur `innerHTML` mein security-difference kya hai?**
A: `textContent` hamesha plain-text treat karta hai, safe hai untrusted content ke liye. `innerHTML` string ko HTML parse karta hai — untrusted input ke saath XSS-vulnerability create kar sakta hai. (Detail: [11-DOM-and-Browser/03-Modifying-Elements-Attributes-Styles.md](../11-DOM-and-Browser/03-Modifying-Elements-Attributes-Styles.md), [23-Security/01-XSS-CSRF-and-Injection.md](../23-Security/01-XSS-CSRF-and-Injection.md))

**Q3: `querySelectorAll` aur `getElementsByClassName` mein kya difference hai?**
A: `getElementsByClassName` **live** collection deta hai (DOM-changes ke saath auto-update). `querySelectorAll` **static** snapshot deta hai. (Detail: [11-DOM-and-Browser/02-Selecting-and-Creating-Elements.md](../11-DOM-and-Browser/02-Selecting-and-Creating-Elements.md))

**Q4: Event bubbling aur capturing mein kya difference hai?**
A: Capturing: event `document` se target ki taraf top-down travel karta hai. Bubbling: target se `document` tak bottom-up (default listening phase). (Detail: [12-Events/02-Bubbling-Capturing-and-Delegation.md](../12-Events/02-Bubbling-Capturing-and-Delegation.md))

**Q5: Event delegation kya hai aur kyun useful hai?**
A: Ek single listener parent pe lagana instead of har child pe — bubbling ka use karke `event.target` check karke decide karte hain. Performance-benefit aur dynamically-added elements automatically kaam karte hain. (Detail: same as above)

**Q6: `localStorage`, `sessionStorage`, cookies mein kab kaunsa use karein?**
A: `localStorage` permanent client-data ke liye. `sessionStorage` sirf tab-session tak. Cookies jab server ko automatically data chahiye (authentication) — `httpOnly` cookies sensitive tokens ke liye best. (Detail: [11-DOM-and-Browser/05-Storage-and-Cookies.md](../11-DOM-and-Browser/05-Storage-and-Cookies.md))

**Q7: `event.preventDefault()` kab use karte hain?**
A: Browser ke default behavior (jaise form-submit pe page-reload) ko cancel karne ke liye — custom JavaScript-logic (validation, fetch-call) chalane ke liye control lena. (Detail: [11-DOM-and-Browser/04-Forms-and-Validation.md](../11-DOM-and-Browser/04-Forms-and-Validation.md))

**Q8: Intersection Observer scroll-events se better kyun hai?**
A: Efficiently visibility-detect karta hai, browser ke rendering-engine se integrate hota hai, `scroll` events ki tarah bahut frequently fire nahi hota — lazy-loading/infinite-scroll ke liye ideal. (Detail: [11-DOM-and-Browser/06-Browser-APIs.md](../11-DOM-and-Browser/06-Browser-APIs.md))

**Q9: Web Workers ka purpose kya hai?**
A: JavaScript ko background thread mein chalane deta hai bina main (UI) thread block kiye — heavy computations ke liye, taaki UI responsive rahe. Workers DOM access nahi kar sakte. (Detail: same as above)

**Q10: `DOMContentLoaded` event kyun important hai?**
A: Ensure karta hai DOM elements manipulate karne se pehle wo actually load ho chuke hain — especially jab script `<head>` mein ho. (Detail: [11-DOM-and-Browser/01-DOM-Basics.md](../11-DOM-and-Browser/01-DOM-Basics.md))

---

**Next:** [07-NodeJS.md](./07-NodeJS.md)
