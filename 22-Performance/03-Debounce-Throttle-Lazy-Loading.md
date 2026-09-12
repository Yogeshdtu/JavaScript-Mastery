# Debounce, Throttle, and Lazy Loading

**Module:** 22-Performance
**Difficulty:** 🔴 Advanced
**Previous:** [02-Memory-and-Garbage-Collection.md](./02-Memory-and-Garbage-Collection.md)
**Next:** [04-Caching-and-Network-Optimization.md](./04-Caching-and-Network-Optimization.md)

---

## 1. Learning Objectives

- Debounce aur Throttle patterns implement karna aur unka difference samajhna.
- Lazy loading ka concept aur implementation (Module 11's Intersection Observer se connected) samajhna.
- Ye samajhna ki closures (Module 08) in patterns ka foundation kaise hain.

## 2. Prerequisites

Module 08 (Closures), Module 12 (Events), Module 11, chapter 06 (Intersection Observer).

## 3. Concept in Simple Hinglish

**Debounce** ek function ko sirf tab chalata hai jab "activity ruk jaaye" ek certain time ke liye — jaise search-box mein type karte waqt, sirf tab search karo jab user typing rok de. **Throttle** ek function ko **maximum ek baar** ek fixed time-interval mein chalata hai, chahe trigger kitni baar bhi ho — jaise scroll event ko har 100ms mein sirf ek baar handle karna.

## 4. Technical Explanation

**Debounce:** Timer ko **reset** karta hai har naye trigger pe — function sirf tab chalta hai jab triggers ruk jaayen aur delay-time pura ho jaaye.

**Throttle:** Function ko **first call ke baad**, ek fixed cooldown-period tak dobara chalne se rokta hai — regular intervals pe execution guarantee karta hai high-frequency events ke dauraan.

## 5. Syntax

```js
function debounce(fn, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}

function throttle(fn, limit) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}
```

## 6. Basic Examples

```js
function search(query) {
  console.log("Searching for:", query);
}

const debouncedSearch = debounce(search, 300);

// Rapidly calling this (like fast typing):
debouncedSearch("a");
debouncedSearch("ap");
debouncedSearch("app"); // Only THIS call actually executes 'search', 300ms after the last keystroke!
```

## 7. Detailed Examples

**Debounce in action — a search input (connecting to Module 11, chapter 04):**
```js
function debounce(fn, delay) {
  let timeoutId; // This variable persists via CLOSURE (Module 08, chapter 04)!
  return function (...args) {
    clearTimeout(timeoutId); // Cancel the previous pending call
    timeoutId = setTimeout(() => fn.apply(this, args), delay); // Schedule a new one
  };
}

function performSearch(query) {
  console.log(`Searching API for: "${query}"`);
  // fetch(`/api/search?q=${query}`)... (Module 16!)
}

const debouncedSearch = debounce(performSearch, 500);

document.getElementById("searchInput").addEventListener("input", (event) => {
  debouncedSearch(event.target.value); // Fires on every keystroke, but the ACTUAL search waits!
});
```
**Why this matters:** Bina debounce ke, har keystroke pe API call trigger hoti — agar user "javascript" type kare (10 characters), **10 API calls** ho sakti hain! Debounce ensure karta hai sirf **ek call**, jab user typing rok de.

**Throttle in action — a scroll event handler:**
```js
function throttle(fn, limit) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      fn.apply(this, args); // Execute immediately on the FIRST call
      inThrottle = true;
      setTimeout(() => { inThrottle = false; }, limit); // Cooldown period
    }
    // Calls DURING the cooldown period are simply ignored
  };
}

function handleScroll() {
  console.log("Scroll position:", window.scrollY);
  // Expensive operation, like updating a "scroll progress" bar
}

const throttledScroll = throttle(handleScroll, 200);
window.addEventListener("scroll", throttledScroll);
// Even if 'scroll' fires 50 times per second, handleScroll runs AT MOST every 200ms
```

**Debounce vs Throttle — visualizing the difference:**
```
Rapid events:     | | | | | | | | | |  (10 events in quick succession)

Debounce (300ms):                    X  (fires ONCE, after events stop for 300ms)

Throttle (300ms):  X       X       X    (fires at REGULAR intervals during the activity)
```

**Lazy Loading — combining with Intersection Observer (Module 11, chapter 06):**
```js
const images = document.querySelectorAll("img[data-src]");

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src; // Load the REAL image only when it's about to be visible!
      img.removeAttribute("data-src");
      observer.unobserve(img); // Stop observing this image — job done
    }
  });
});

images.forEach(img => observer.observe(img));
```
```html
<!-- Placeholder src initially — real image URL stored in data-src -->
<img src="placeholder.jpg" data-src="actual-large-image.jpg" alt="...">
```
Ye pattern **initial page-load time** dramatically improve karta hai — agar page pe 50 images hain, sabko turant load karne ke bajaye, sirf wo images load hoti hain jo user actually scroll karke dekhne wala hai.

## 8. Mental Model

Debounce ko socho **elevator door** — har naya person (event) door ko band hone se rokta hai (timer reset), door sirf tab band hota hai (function executes) jab thodi der tak koi naya person na aaye. Throttle ko socho **ek machine jo har X seconds mein sirf ek photo click kar sakti hai** — chahe kitne bhi log "click" bolein beech mein, camera sirf apne fixed interval pe hi actually click karega.

## 9. What Happens Internally?

Dono patterns **closures** (Module 08, chapter 04) use karte hain — `timeoutId` (debounce) ya `inThrottle` (throttle) variable returned inner-function ke through "remembered" rehta hai across multiple calls, jo state maintain karne deta hai bina kisi external/global variable ke.

## 10. Common Mistakes

- Debounce aur Throttle ko interchangeably use karna jab unke use-cases different hain — search-input debounce chahta hai, scroll-tracking throttle.
- Debounce/throttle delay-value ko bahut zyada/kam set karna — UX ko affect karta hai (bahut zyada delay slow-feeling, bahut kam delay performance-benefit nahi deta).
- Lazy loading ko above-the-fold (turant visible) content pe apply karna — usse turant dikhna chahiye, lazy-load karne se initial content late dikhega.

## 11. Edge Cases

Debounce ka ek variant "immediate" (leading-edge) execution bhi support kar sakta hai — pehla call turant execute ho, baaki calls debounce ho jayein — dono variants (leading vs trailing) real-world libraries (Lodash) mein available hain.

## 12. Real-World Usage

Debounce: search-boxes, form-auto-save, resize-event-handlers. Throttle: scroll-tracking, infinite-scroll-trigger, mouse-move-based animations. Lazy loading: image-heavy websites, infinite-scroll content, route-based code-splitting (Module 17, chapter 01's dynamic imports also relate here).

## 13. Comparison With Related Concepts

| Pattern | Behavior | Best For |
|---------|-----------|--------------|
| Debounce | Waits for "quiet period" before executing | Search input, auto-save |
| Throttle | Executes at most once per interval | Scroll, resize, mouse-move |
| Lazy Loading | Delays loading until needed | Images, below-the-fold content |

## 14. Practice Questions

1. Debounce aur Throttle mein fundamental behavior-difference kya hai?
2. Search-input ke liye kaunsa pattern (debounce/throttle) zyada appropriate hai, aur kyun?
3. Lazy loading initial page-load performance ko kaise improve karta hai?

## 15. Challenge

Ek `throttle` function implement karo (agar upar wala pattern samajh gaya, khud se likh ke dekho bina reference dekhe), aur isse ek window-resize event-handler pe apply karo jo console mein current window-dimensions log kare, max ek baar per 500ms.

## 16. Interview Questions

**Q1: Debounce aur Throttle mein kya difference hai, real-world examples ke saath?**
A: **Debounce** ek function ke execution ko delay karta hai jab tak "trigger-events rukna" na band ho jaayein — har naya trigger timer ko reset kar deta hai. Ye tab useful hai jab tumhe sirf **final** state se matlab ho (jaise search-input — user ke typing complete karne ke baad hi API call karni hai, har keystroke pe nahi). **Throttle** function ko **regular intervals** pe execute hone deta hai, chahe trigger kitni bhi frequently ho — ye tab useful hai jab tumhe **periodic updates** chahiye continuous activity ke dauraan (jaise scroll-position track karna — har scroll-pixel pe nahi, but har 100-200ms mein ek baar check karna kaafi hai).

**Q2: Lazy loading kya hai aur ye web-performance kaise improve karta hai?**
A: Lazy loading ek technique hai jisme resources (usually images, ya components/routes) **turant load nahi** kiye jaate — unhe tab load kiya jaata hai jab wo actually zaroorat mein hon (jaise user unhe scroll karke viewport tak le aaye). Isse **initial page-load time** significantly kam hota hai, kyunki browser ko turant sirf wahi content download karna padta hai jo immediately visible hai — baaki content "on-demand" load hota hai (typically Intersection Observer, Module 11 chapter 06 use karke). Ye especially image-heavy ya content-heavy websites ke liye bahut effective performance-optimization hai.

## 17. Chapter Summary

Debounce function-execution ko delay karta hai jab tak activity ruk na jaaye (search-inputs ke liye ideal). Throttle function ko fixed intervals pe execute hone deta hai continuous-activity ke dauraan (scroll/resize ke liye ideal). Dono patterns closures use karte hain state maintain karne ke liye. Lazy loading (Intersection Observer ke saath) initial page-load performance ko resources ko on-demand load karke improve karta hai.

## 18. Revision Checklist

- [ ] Debounce vs Throttle ka behavior-difference clear hai with examples.
- [ ] Closures in patterns ka foundation kaise hain, samajh gaya.
- [ ] Lazy loading ka Intersection Observer ke saath implementation yaad hai.

---

**Next:** [04-Caching-and-Network-Optimization.md](./04-Caching-and-Network-Optimization.md)
