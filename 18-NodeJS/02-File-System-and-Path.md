# File System and Path Modules

**Module:** 18-NodeJS
**Difficulty:** 🟡 Intermediate
**Previous:** [01-Node-Architecture-and-NPM.md](./01-Node-Architecture-and-NPM.md)
**Next:** [03-Process-and-Environment-Variables.md](./03-Process-and-Environment-Variables.md)

---

## 1. Learning Objectives

- `fs` module se files read/write karna (sync aur async dono tareeke se).
- `path` module se cross-platform file paths handle karna.
- Callback, Promise, aur sync APIs ke beech sahi choice karna.

## 2. Prerequisites

[01-Node-Architecture-and-NPM.md](./01-Node-Architecture-and-NPM.md)

## 3. Concept in Simple Hinglish

`fs` (File System) module Node.js ko files **read/write/delete** karne deta hai — ye capability browser mein security reasons se nahi hai (Module 11 se yaad karo, browser JavaScript directly file system access nahi kar sakti). `path` module file paths ko **cross-platform** (Windows/Mac/Linux compatible) tareeke se handle karne mein help karta hai.

## 4. Technical Explanation

**`fs` module ke 3 API styles:**
1. **Synchronous:** `fs.readFileSync()` — blocking, simple, but poore application ko rok deta hai jab tak complete na ho.
2. **Callback-based (⚠️ legacy style):** `fs.readFile(path, callback)` — non-blocking, error-first callback pattern (Module 13, chapter 02 se yaad karo).
3. **Promise-based (✅ modern):** `fs.promises.readFile()` ya `require("fs/promises")` — non-blocking, `async`/`await` ke saath naturally kaam karta hai.

## 5. Syntax

```js
const fs = require("fs");
const fsPromises = require("fs/promises");
const path = require("path");

fs.readFileSync(filePath, "utf-8");             // Sync
fs.readFile(filePath, "utf-8", callback);        // Callback-based
await fsPromises.readFile(filePath, "utf-8");     // Promise-based (modern)

path.join(dir1, dir2, file);
path.resolve(relativePath);
```

## 6. Basic Examples

```js
const fs = require("fs/promises");

async function readMyFile() {
  const content = await fs.readFile("data.txt", "utf-8");
  console.log(content);
}
readMyFile();
```

## 7. Detailed Examples

**Comparing all 3 styles for the same task:**
```js
const fs = require("fs");
const fsPromises = require("fs/promises");

// ⚠️ Synchronous — blocks everything until done (use sparingly, e.g., for CLI startup scripts)
try {
  const data = fs.readFileSync("config.json", "utf-8");
  console.log(JSON.parse(data));
} catch (error) {
  console.log("Error reading file:", error.message);
}

// ⚠️ Callback-based (legacy) — non-blocking, but leads to callback-hell for multiple operations
fs.readFile("config.json", "utf-8", (error, data) => {
  if (error) {
    console.log("Error:", error.message);
    return;
  }
  console.log(JSON.parse(data));
});

// ✅ Promise-based (modern) — non-blocking, clean with async/await
async function readConfig() {
  try {
    const data = await fsPromises.readFile("config.json", "utf-8");
    return JSON.parse(data);
  } catch (error) {
    console.log("Error:", error.message);
  }
}
```

**Writing files and checking existence:**
```js
const fsPromises = require("fs/promises");

async function saveUserData(user) {
  await fsPromises.writeFile("user.json", JSON.stringify(user, null, 2));
  console.log("File saved!");
}

async function checkFileExists(filePath) {
  try {
    await fsPromises.access(filePath);
    return true;
  } catch {
    return false;
  }
}

saveUserData({ name: "Anaya", age: 22 });
```

**Working with directories:**
```js
const fsPromises = require("fs/promises");

async function listFiles(directory) {
  const files = await fsPromises.readdir(directory);
  console.log(files); // Array of file/folder names
}

async function createFolder(folderName) {
  await fsPromises.mkdir(folderName, { recursive: true }); // recursive: create nested dirs if needed
}
```

**The `path` module — writing cross-platform-safe code:**
```js
const path = require("path");

// ⚠️ Never do this — breaks on different operating systems!
// const filePath = folder + "/" + fileName; // Windows uses \, this assumes /

// ✅ Correct — path.join() handles OS differences automatically
const filePath = path.join("data", "users", "profile.json");
console.log(filePath); // "data/users/profile.json" (or "data\users\profile.json" on Windows)

console.log(path.basename("/home/user/file.txt")); // "file.txt"
console.log(path.dirname("/home/user/file.txt"));   // "/home/user"
console.log(path.extname("/home/user/file.txt"));    // ".txt"
console.log(path.resolve("data", "file.txt"));         // Full absolute path from current working directory

// __dirname — a super common pattern to build paths relative to the CURRENT file
const configPath = path.join(__dirname, "config.json");
```

## 8. Mental Model

`fs` module ko socho ek **file clerk** jo tumhare liye files read/write karta hai. Synchronous version ek clerk hai jo **tumhare saamne khada rehta hai** jab tak kaam khatam na ho (tum kuch aur nahi kar sakte). Async versions (callback/Promise) ek clerk hain jo kaam **background mein** karte hain aur jab ready ho tumhe batate hain, tab tak tum doosra kaam kar sakte ho. `path` module ek **universal translator** hai jo file-addresses (paths) ko har operating system ke apne format mein correctly likhta hai.

## 9. What Happens Internally?

File operations Node.js mein **libuv** (Module 18, chapter 01 se yaad karo) ke through non-blocking tareeke se handle hoti hain — actual disk I/O ek **thread pool** mein background mein hoti hai, jab complete hoti hai, callback/Promise ko Event Loop ke through main thread pe wapas laaya jaata hai (Module 15 ke concepts directly applicable hain, sirf Web APIs ki jagah libuv hai).

## 10. Common Mistakes

- Production server code mein synchronous `fs` methods use karna — pura server block ho jaata hai file-operation ke duration ke liye.
- File paths ko manually string-concatenation se banana (`folder + "/" + file`) — OS-specific bugs create karta hai.
- File existence check karne ke liye purane `fs.exists()` (deprecated) use karna — `fs.access()` ya try/catch with `readFile` use karo.

## 11. Edge Cases

```js
const fsPromises = require("fs/promises");

async function readOptionalFile(path) {
  try {
    return await fsPromises.readFile(path, "utf-8");
  } catch (error) {
    if (error.code === "ENOENT") { // "Error NO ENTry" — file doesn't exist
      return null; // Gracefully handle missing file
    }
    throw error; // Re-throw unexpected errors
  }
}
```

## 12. Real-World Usage

`fs` module configuration files read karne, logs likhne, uploaded files process karne, static assets serve karne mein use hota hai. `path` module almost har Node.js application mein file-path construction ke liye standard hai — especially `__dirname`-based patterns.

## 13. Comparison With Related Concepts

| API Style | Blocking? | Recommended For |
|-----------|-------------|----------------------|
| Sync (`readFileSync`) | Yes | Quick CLI scripts, startup config loading |
| Callback (`readFile` + callback) | No | ⚠️ Legacy code |
| Promise (`fs/promises`) | No | ✅ Modern applications |

## 14. Practice Questions

1. `fs.readFileSync()` production servers mein risky kyun hai?
2. `path.join()` string concatenation se better kyun hai?
3. `fs.readFile()` ke error mein `error.code === "ENOENT"` ka matlab kya hai?

## 15. Challenge

Ek script likho jo `data/` folder banaye (agar exist nahi karta), usme ek `notes.json` file likhe (array of notes ke saath), phir usse wapas read karke console mein print kare — sab `fs/promises` aur `path` use karke.

## 16. Interview Questions

**Q1: Synchronous file operations (`fs.readFileSync`) production Node.js servers mein kyun avoid ki jaati hain?**
A: Node.js **single-threaded** hai (Module 15 se yaad karo) — synchronous operations poore thread ko **block** kar dete hain jab tak wo complete na ho. Agar ek server request handle karte waqt `fs.readFileSync()` use kare aur file-read mein time lage, **sabhi doosre incoming requests bhi block ho jaayenge** us duration ke liye, chahe wo file-operation se related na ho. Isi liye production servers async APIs (`fs.promises` ya callback-based) use karte hain, taaki ek slow operation baaki requests ko affect na kare.

**Q2: `path.join()` manual string concatenation se better kyun hai file paths banane ke liye?**
A: File path separators operating systems ke beech different hote hain — Unix/Mac `/` use karte hain, Windows `\` use karta hai. Manual concatenation (`folder + "/" + file`) hardcode kar deta hai ek specific separator, jo doosre OS pe break ho sakta hai. `path.join()` automatically current operating system ke correct separator ka use karta hai, aur extra edge cases (jaise duplicate slashes, relative segments `..`) ko bhi correctly normalize karta hai — isse code truly cross-platform ban jaata hai.

## 17. Chapter Summary

`fs` module file operations ke liye 3 styles deta hai — sync (blocking, use sparingly), callback (⚠️ legacy), Promise-based (✅ modern, async/await-friendly). `path` module cross-platform-safe file paths banata hai — `path.join()`, `__dirname` common patterns hain. Async file operations libuv ke thread-pool ke through non-blocking hoti hain.

## 18. Revision Checklist

- [ ] 3 `fs` API styles aur unke trade-offs yaad hain.
- [ ] `path.join()` ka cross-platform benefit clear hai.
- [ ] Async file-reading pattern (with error handling) likh sakta hoon.

---

**Next:** [03-Process-and-Environment-Variables.md](./03-Process-and-Environment-Variables.md)
