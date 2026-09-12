# Events, Streams, and Buffers

**Module:** 18-NodeJS
**Difficulty:** 🟠 Advanced
**Previous:** [03-Process-and-Environment-Variables.md](./03-Process-and-Environment-Variables.md)
**Next:** [05-HTTP-and-CLI-Apps.md](./05-HTTP-and-CLI-Apps.md)

---

## 1. Learning Objectives

- `EventEmitter` se custom events banana aur handle karna.
- Streams ka concept aur unka memory-efficiency benefit samajhna.
- Buffers ka basic purpose samajhna (binary data).

## 2. Prerequisites

Module 18, chapters 01-03. Module 12 (Events) ke concepts helpful hain.

## 3. Concept in Simple Hinglish

**`EventEmitter`** Node.js ka built-in pattern hai custom events banane ke liye (Module 12 mein DOM events dekhe the, ye Node.js ka apna version hai). **Streams** bade data (jaise large files) ko **chunks mein process** karne ka tareeka hain, poora ek saath memory mein load kiye bina. **Buffers** raw binary data represent karte hain (jaise image files, network packets).

## 4. Technical Explanation

**`EventEmitter`:** Ek class jise extend/instantiate karke apne custom events emit/listen kar sakte ho — Node.js ka bahut sara core API (streams, HTTP servers) internally `EventEmitter` pe based hai.

**Streams:** 4 types — `Readable` (data source, jaise file read), `Writable` (data destination, jaise file write), `Duplex` (dono), `Transform` (data ko process karte hue pass karta hai).

**Buffer:** Fixed-size raw memory allocation, binary data ke liye — JavaScript arrays text/objects ke liye best hain, Buffers binary (images, network data) ke liye.

## 5. Syntax

```js
const EventEmitter = require("events");
const emitter = new EventEmitter();
emitter.on("eventName", callback);
emitter.emit("eventName", data);

const fs = require("fs");
const readStream = fs.createReadStream("file.txt");
const writeStream = fs.createWriteStream("output.txt");
```

## 6. Basic Examples

```js
const EventEmitter = require("events");
const myEmitter = new EventEmitter();

myEmitter.on("greet", (name) => {
  console.log(`Hello, ${name}!`);
});

myEmitter.emit("greet", "Rhea"); // "Hello, Rhea!"
```

## 7. Detailed Examples

**Building a custom class with `EventEmitter` — a common Node.js pattern:**
```js
const EventEmitter = require("events");

class OrderProcessor extends EventEmitter {
  processOrder(order) {
    console.log(`Processing order ${order.id}...`);
    // Simulate processing
    setTimeout(() => {
      if (order.amount > 0) {
        this.emit("success", order);
      } else {
        this.emit("error", new Error("Invalid order amount"));
      }
    }, 500);
  }
}

const processor = new OrderProcessor();

processor.on("success", (order) => console.log(`Order ${order.id} completed!`));
processor.on("error", (error) => console.log(`Failed: ${error.message}`));

processor.processOrder({ id: 1, amount: 500 });
processor.processOrder({ id: 2, amount: -10 });
```

**Why streams matter — the memory problem they solve:**
```js
const fs = require("fs");

// ⚠️ Problem approach — loads the ENTIRE file into memory at once
fs.readFile("huge-video-file.mp4", (err, data) => {
  // If the file is 5GB, this tries to load ALL 5GB into memory — could crash the process!
});

// ✅ Streaming approach — processes data in small chunks
const readStream = fs.createReadStream("huge-video-file.mp4");
const writeStream = fs.createWriteStream("copy.mp4");

readStream.pipe(writeStream); // Reads a chunk, writes it, reads next chunk, writes it... automatically!
console.log("Copying started (memory-efficiently)...");
```
`pipe()` automatically chunks ko read-stream se write-stream mein transfer karta hai, ek chhota buffer maintain karte hue — poori file kabhi bhi ek saath memory mein nahi hoti. Ye large files (videos, logs, database exports) ke liye **essential** hai.

**Listening to stream events directly:**
```js
const readStream = fs.createReadStream("data.txt", { encoding: "utf-8" });

let content = "";
readStream.on("data", (chunk) => {
  console.log(`Received a chunk of ${chunk.length} characters`);
  content += chunk;
});

readStream.on("end", () => {
  console.log("Finished reading file. Total length:", content.length);
});

readStream.on("error", (error) => {
  console.log("Stream error:", error.message);
});
```

**Buffers — working with binary data:**
```js
const buffer = Buffer.from("Hello", "utf-8");
console.log(buffer);            // <Buffer 48 65 6c 6c 6f> — raw bytes in hexadecimal
console.log(buffer.toString());  // "Hello" — converting back to a readable string
console.log(buffer.length);       // 5 (bytes)

const buffer2 = Buffer.alloc(10); // Allocates 10 bytes, initialized to zero
```
Buffers tab relevant hain jab tum raw binary data handle kar rahe ho — images, file uploads, network protocols — jaha data text nahi hai, raw bytes hain.

## 8. Mental Model

`EventEmitter` ko socho ek **radio station** — station events "broadcast" (emit) karta hai, aur jo bhi "tune in" (listen, `.on()`) kiye hue hain, wo receive karte hain. Streams ko socho ek **paani ki pipeline** — poora paani ek saath store karne ke bajaye (bade tank ki tarah), pipeline se continuously flow hota hai, chhote-chhote amounts mein — memory-efficient hai bade volumes ke liye. Buffer ko socho **raw ingredients ka ek box** jo abhi tak "readable text" mein convert nahi hua — jaise ek sealed packet jise kholna (parse karna) padta hai.

## 9. What Happens Internally?

Streams internally ek **internal buffer** maintain karte hain jisme chunks temporarily store hote hain jab tak consumer (jaise write-stream) unhe process nahi kar leta — ye "backpressure" mechanism ensure karta hai ki fast producer, slow consumer ko overwhelm na kare. `EventEmitter` internally ek listeners-map maintain karta hai per-event-name, aur `emit()` call hone pe sequentially sabhi registered listeners ko synchronously call karta hai.

## 10. Common Mistakes

- Bade files ke liye `readFile()` (poori file load) use karna jab `createReadStream()` (chunked) better hota.
- `EventEmitter` mein bahut saare listeners add karna aur "MaxListenersExceededWarning" milna (default limit 10 hai, `setMaxListeners()` se change ho sakta hai).
- Buffers ko regular strings/arrays jaisa treat karne ki koshish karna bina samjhe ki ye raw bytes hain.

## 11. Edge Cases

```js
const emitter = new EventEmitter();
emitter.on("error", () => {}); // ⚠️ IMPORTANT: Always handle 'error' events!
// If an EventEmitter emits 'error' with NO listener attached, Node.js will CRASH the process!
```

## 12. Real-World Usage

Streams video/audio streaming services, log-file processing, large CSV/database exports mein essential hain. `EventEmitter` Node.js ke core modules (HTTP servers, streams themselves) ka foundation hai, aur custom application-level event systems (jaise order-processing workflows) mein use hota hai.

## 13. Comparison With Related Concepts

| Concept | Similar To (Module 12) |
|---------|------------------------------|
| `EventEmitter` | DOM's `addEventListener`/dispatch mechanism, but for non-DOM Node.js code |
| Streams | Conceptually like "lazy" iteration (Module 10, chapter 05 — generators) but for I/O data |

## 14. Practice Questions

1. Streams bade files ke liye `readFile()` se better kyun hain?
2. `EventEmitter` mein 'error' event ka listener na hone se kya risk hai?
3. Buffer kya represent karta hai?

## 15. Challenge

Ek `EventEmitter`-based class `DownloadTracker` banao jisme `startDownload()`, `updateProgress(percent)` methods hon jo respectively `"start"` aur `"progress"` events emit karein, aur `100%` pe `"complete"` event emit ho. Listeners attach karke test karo.

## 16. Interview Questions

**Q1: Node.js Streams ka main advantage kya hai large files handle karte waqt?**
A: Streams data ko **chunks mein process** karte hain, poori file ko ek saath memory mein load kiye bina. Agar tum `fs.readFile()` se ek 5GB file load karo, poore 5GB memory mein aa jaayenge (crash ka risk). Streams (`fs.createReadStream()`) sirf ek chhota chunk memory mein rakhte hain kisi bhi time — data continuously "flow" karta hai source se destination tak (`pipe()` ke through), jisse memory usage constant aur predictable rehta hai chahe file kitni bhi badi ho.

**Q2: `EventEmitter` mein 'error' event ke liye listener na hona kyun dangerous hai?**
A: Node.js mein `EventEmitter` ke liye special rule hai — agar `'error'` event emit hoti hai aur uske liye **koi listener registered nahi hai**, Node.js is situation ko treat karta hai jaise ek **uncaught exception** hui ho, aur **process crash** kar deta hai. Ye deliberate design choice hai taaki errors silently ignore na ho jaayen. Isi liye best practice hai har `EventEmitter`-based object pe hamesha ek `'error'` listener attach karna, chahe wo sirf logging ke liye ho.

## 17. Chapter Summary

`EventEmitter` Node.js ka custom-event-system foundation hai (`.on()`, `.emit()`). Streams large data ko memory-efficiently, chunk-by-chunk process karne dete hain — `pipe()` common pattern hai. Buffers raw binary data represent karte hain jab data text-based nahi hoti (images, network packets).

## 18. Revision Checklist

- [ ] `EventEmitter` ka basic pattern (`on`/`emit`) likh sakta hoon.
- [ ] Streams ka memory-efficiency benefit clear hai.
- [ ] 'error' event listener ki criticality yaad hai.

---

**Next:** [05-HTTP-and-CLI-Apps.md](./05-HTTP-and-CLI-Apps.md)
