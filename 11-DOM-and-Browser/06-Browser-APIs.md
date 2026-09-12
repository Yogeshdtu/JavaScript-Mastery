# Browser APIs — URL, History, Clipboard, File, Observers, Web Workers, WebSockets

**Module:** 11-DOM-and-Browser
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [05-Storage-and-Cookies.md](./05-Storage-and-Cookies.md)
**Next:** [12-Events/01-Event-Basics-and-Listeners.md](../12-Events/01-Event-Basics-and-Listeners.md)

---

## 1. Learning Objectives

- Modern browser APIs ka overview paana: URL, History, Clipboard, File, Intersection/Mutation Observer, Web Workers, WebSockets.
- Har API ka basic use-case aur syntax samajhna.

## 2. Prerequisites

Module 11, chapters 01-05.

## 3. Concept in Simple Hinglish

Browsers sirf HTML render nahi karte — wo bahut saari **built-in capabilities (APIs)** provide karte hain jo JavaScript use kar sakti hai: URL parse karna, browser history control karna, clipboard access karna, files handle karna, aur bahut kuch. Ye chapter in sabka **practical overview** deta hai.

## 4. Technical Explanation

Har API ek specific browser capability expose karta hai `window` object ke through (ya global scope mein directly available).

## 5. Syntax

```js
new URL(urlString);
history.pushState(state, title, url);
navigator.clipboard.writeText(text);
new IntersectionObserver(callback);
new MutationObserver(callback);
new Worker("worker.js");
new WebSocket(url);
```

## 6. Basic Examples

```js
const url = new URL("https://example.com/page?id=5");
console.log(url.searchParams.get("id")); // "5"
```

## 7. Detailed Examples

**URL API — parsing and building URLs safely:**
```js
const url = new URL("https://shop.example.com/products?category=shoes&sort=price");

console.log(url.hostname);      // "shop.example.com"
console.log(url.pathname);       // "/products"
console.log(url.searchParams.get("category")); // "shoes"

url.searchParams.set("page", "2");
console.log(url.toString()); // "https://shop.example.com/products?category=shoes&sort=price&page=2"
```
**✅ Modern / Recommended:** URL API string-concatenation-based URL building se bahut safer/reliable hai (encoding automatically handle hota hai).

**History API — navigation without page reload (SPA foundation):**
```js
history.pushState({ page: "profile" }, "", "/profile"); // Changes URL WITHOUT reloading the page

window.addEventListener("popstate", (event) => {
  console.log("Navigated back/forward:", event.state);
});
```
Ye mechanism Single Page Applications (React Router jaisi libraries) ka foundation hai — URL change hoti hai bina full page reload ke.

**Clipboard API — copy to clipboard (modern, permission-based):**
```js
async function copyText(text) {
  try {
    await navigator.clipboard.writeText(text);
    console.log("Copied to clipboard!");
  } catch (err) {
    console.log("Failed to copy:", err);
  }
}
copyText("Hello, World!");
```

**File API — reading user-selected files:**
```js
const fileInput = document.querySelector('input[type="file"]');

fileInput.addEventListener("change", (event) => {
  const file = event.target.files[0];
  const reader = new FileReader();

  reader.onload = () => {
    console.log(reader.result); // File content (as text, if readAsText used)
  };

  reader.readAsText(file);
});
```

**Intersection Observer — efficient scroll-based detection (lazy loading, infinite scroll):**
```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      console.log("Element is now visible!", entry.target);
      // Common use: lazy-load an image here
    }
  });
});

const targetElement = document.querySelector("#lazy-image");
observer.observe(targetElement);
```
**✅ Modern / Recommended:** Intersection Observer scroll-event-listener-based visibility detection se **bahut zyada performant** hai (Module 22 mein performance detail) — engine internally optimize karta hai, tumhe manually scroll position calculate nahi karni padti.

**Mutation Observer — watching DOM changes:**
```js
const targetNode = document.getElementById("content");

const mutationObserver = new MutationObserver((mutationsList) => {
  for (const mutation of mutationsList) {
    console.log("DOM changed:", mutation.type);
  }
});

mutationObserver.observe(targetNode, { childList: true, subtree: true });
```

**Web Workers — running JavaScript in a background thread:**
```js
// main.js
const worker = new Worker("worker.js");
worker.postMessage({ number: 42 });
worker.onmessage = (event) => {
  console.log("Result from worker:", event.data);
};

// worker.js (separate file)
// self.onmessage = (event) => {
//   const result = event.data.number * 2;
//   self.postMessage(result);
// };
```
Web Workers heavy computations ko **main thread ko block kiye bina** background mein run karne dete hain — Module 15 (Event Loop) ke single-threaded nature ka ye ek "escape hatch" hai.

**WebSockets — real-time, bidirectional communication (preview, more in Module 27 projects):**
```js
const socket = new WebSocket("wss://example.com/chat");

socket.onopen = () => console.log("Connected!");
socket.onmessage = (event) => console.log("Received:", event.data);
socket.send("Hello, server!");
```

## 8. Mental Model

In sab APIs ko socho browser ke **built-in toolkit** ki tarah — jaise ek Swiss Army knife jisme URL-parsing scissors, clipboard tweezers, file-reading magnifying glass, aur real-time-communication walkie-talkie (WebSocket) sab included hain. Web Workers ko socho ek **dusra kamra jaha heavy kaam ho sakta hai** bina main room (UI thread) ko disturb kiye.

## 9. What Happens Internally?

Intersection/Mutation Observers browser engine ke internal rendering/DOM-update pipeline se directly hook karte hain — isi liye ye manual polling (`setInterval` se check karna) se zyada efficient hain. Web Workers apna **separate JavaScript execution context aur call stack** rakhte hain — main thread se completely isolated, communication sirf message-passing (`postMessage`) se hoti hai (koi shared memory by default).

## 10. Common Mistakes

- Intersection Observer ki jagah manual `scroll` event listeners use karna performance-heavy scenarios mein.
- Web Workers ke andar directly DOM access karne ki koshish karna — Workers ke paas DOM access **nahi** hota (sirf main thread ke paas hai).
- Clipboard API ko permission-handling ke bina use karna — modern browsers user-permission/secure-context (HTTPS) require karte hain.

## 11. Edge Cases

Web Workers `import`/`require` directly support nahi karte purane tareeke se — modern workers ES module syntax support karte hain with `{ type: "module" }` option. Clipboard API sirf **secure contexts** (HTTPS ya localhost) mein kaam karta hai.

## 12. Real-World Usage

URL API query-parameter-heavy applications (e-commerce filters) mein. History API SPAs mein. Intersection Observer infinite-scroll aur lazy-loading images mein. Web Workers image-processing/data-crunching apps mein. WebSockets live-chat, real-time-notifications, collaborative-editing apps mein (Module 27 projects).

## 13. Comparison With Related Concepts

| API | Primary Use Case |
|-----|----------------------|
| URL | Parsing/building URLs safely |
| History | SPA navigation without reload |
| Clipboard | Copy-to-clipboard features |
| File | Reading user-uploaded files |
| Intersection Observer | Efficient visibility/scroll detection |
| Mutation Observer | Watching DOM changes |
| Web Workers | Background/heavy computation |
| WebSockets | Real-time bidirectional communication |

## 14. Practice Questions

1. Intersection Observer scroll-event-listeners se better kyun hai performance ke liye?
2. Web Workers DOM access kyun nahi kar sakte?
3. WebSockets aur regular HTTP requests mein basic difference kya hai?

## 15. Challenge

Ek Intersection Observer setup karo jo page pe multiple images ko "lazy load" kare — jab image viewport mein aaye, tab uski `src` attribute ek placeholder se actual image URL mein change ho (`data-src` attribute use karke).

## 16. Interview Questions

**Q1: Intersection Observer API ka use-case kya hai aur ye scroll event se better kyun hai?**
A: Intersection Observer efficiently detect karta hai jab koi element viewport mein enter/exit karta hai — common use-cases hain lazy-loading images, infinite scroll, aur scroll-triggered animations. Ye traditional `scroll` event listeners se **better performance** deta hai kyunki ye browser ke rendering engine se directly integrate hota hai aur asynchronously, batched tarike se callbacks fire karta hai — jabki `scroll` events bahut frequently fire hote hain aur manual position-calculation (`getBoundingClientRect()`) expensive ho sakti hai agar har scroll event pe ki jaaye.

**Q2: Web Worker kya hai aur ye kab use karna chahiye?**
A: Web Worker JavaScript code ko ek **separate background thread** mein run karne deta hai, main (UI) thread se independent — isse heavy computations (jaise large-data processing, image manipulation) main thread ko block nahi karti, UI responsive rehti hai. Workers DOM access nahi kar sakte aur main thread se sirf `postMessage()`/`onmessage` ke through communicate karte hain (message-passing, no shared memory by default). Use karo jab koi CPU-intensive task ho jo UI freeze kar sakta ho agar main thread pe chale.

## 17. Chapter Summary

Browsers modern JavaScript applications ke liye rich APIs provide karte hain — URL/History (navigation), Clipboard/File (user-interaction), Intersection/Mutation Observers (efficient DOM-watching), Web Workers (background computation), aur WebSockets (real-time communication). In APIs ka appropriate use professional web applications ki performance aur functionality significantly improve karta hai.

## 18. Revision Checklist

- [ ] URL aur History API ka basic use-case yaad hai.
- [ ] Intersection Observer ka performance-benefit clear hai.
- [ ] Web Workers ka purpose aur limitation (no DOM access) samajh gaya.

---

**Module 11 Complete!** Next Module: [12-Events/01-Event-Basics-and-Listeners.md](../12-Events/01-Event-Basics-and-Listeners.md)
