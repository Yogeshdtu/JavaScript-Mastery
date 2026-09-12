# Module 18 — Node.js

**Difficulty:** 🟠 Intermediate-Advanced
**Chapters:** 5
**Estimated time:** 7-10 hours
**Prerequisites:** [Module 17 (Modules)](../17-Modules/00-Module-Overview.md), [Module 14 (async/await)](../14-Promises-and-Async-Await/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Ab tak JavaScript browser mein chal rahi thi. **Node.js** wo runtime hai jo JavaScript ko tumhare computer/server pe chalata hai — files padhne, servers banane, aur backend applications likhne ke liye.

Yahin se tum "frontend developer" se **full-stack developer** ki taraf move karte ho. Same language, naya environment.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [Node Architecture and NPM](./01-Node-Architecture-and-NPM.md) | V8 + libuv, `package.json`, npm install |
| 02 | [File System and Path](./02-File-System-and-Path.md) | Files padhna/likhna, cross-platform paths |
| 03 | [Process & Environment Variables](./03-Process-and-Environment-Variables.md) | `process.env`, CLI arguments, secrets |
| 04 | [Events, Streams, Buffers](./04-Events-Streams-and-Buffers.md) | EventEmitter, bade data ko chunks mein handle karna |
| 05 | [HTTP and CLI Apps](./05-HTTP-and-CLI-Apps.md) | Pehla server, command-line tools |

---

## Is Module Ke Baad Tum

- [ ] Apne computer pe JavaScript files run kar paoge (`node app.js`).
- [ ] npm packages install aur use kar paoge.
- [ ] Files read/write kar paoge.
- [ ] Ek basic HTTP server bana paoge, bina kisi framework ke.
- [ ] Secrets ko `.env` mein rakhoge, code mein nahi.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **Browser APIs Node mein nahi hain** — `window`, `document`, `localStorage` yahan exist nahi karte. Node ke apne APIs hain (`fs`, `path`, `process`). (Chapter 01)
- **⚠️ Sync methods production mein mat use karo** — `fs.readFileSync` poora server block kar deta hai jab tak file na padh le. Async versions use karo. (Chapter 02)
- **Paths manually join mat karo** — `"folder" + "/" + "file"` Windows pe tootta hai. `path.join()` use karo. (Chapter 02)
- **`.env` file ko kabhi git mein commit mat karna** — `.gitignore` mein daalo. (Chapter 03)
- **`dependencies` vs `devDependencies`** — production mein kya chahiye vs sirf development mein. (Chapter 01)

---

## Practice

- **Projects:** [REST API](../27-Projects/Advanced/02-REST-API/README.md) (Module 19 ke baad)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/13-NodeJS.md](../30-Revision/Cheat-Sheets/13-NodeJS.md)
- **Interview prep:** [29-Interview-Preparation/07-NodeJS.md](../29-Interview-Preparation/07-NodeJS.md)

---

**Shuru karo:** [01-Node-Architecture-and-NPM.md](./01-Node-Architecture-and-NPM.md)
**Agla Module:** [19-Backend-JavaScript](../19-Backend-JavaScript/00-Module-Overview.md)
