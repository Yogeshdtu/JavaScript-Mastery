# HTTP Servers and CLI Apps (Node.js Basics)

**Module:** 18-NodeJS
**Difficulty:** 🟠 Advanced
**Previous:** [04-Events-Streams-and-Buffers.md](./04-Events-Streams-and-Buffers.md)
**Next:** [19-Backend-JavaScript/01-HTTP-Servers-and-Routing.md](../19-Backend-JavaScript/01-HTTP-Servers-and-Routing.md)

---

## 1. Learning Objectives

- Node.js ke built-in `http` module se ek basic server banana.
- Ek simple CLI (Command-Line Interface) tool banana.
- Ye samajhna ki frameworks (jaise Express, Module 19) is raw `http` module ke upar kya value add karte hain.

## 2. Prerequisites

Module 18, chapters 01-04.

## 3. Concept in Simple Hinglish

Node.js ka built-in `http` module bina kisi external library ke ek **web server** banane deta hai — ye Module 19 mein seekhe jaane wale frameworks (Express, etc.) ke "neeche" jo actually ho raha hai, uski buniyaad hai. CLI apps wo scripts hain jo terminal se directly chalti hain, arguments/input lekar kuch process karti hain.

## 4. Technical Explanation

`http.createServer(requestListener)` ek server object banata hai jo **har incoming request pe callback call karta hai** (`EventEmitter` pattern se related — previous chapter). Server ko `.listen(port)` se start kiya jaata hai.

## 5. Syntax

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.end("Hello, World!");
});

server.listen(3000, () => console.log("Server running on port 3000"));
```

## 6. Basic Examples

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader("Content-Type", "text/plain");
  res.end("Hello from Node.js!");
});

server.listen(3000);
```

## 7. Detailed Examples

**A basic router — handling different URLs and methods manually:**
```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.setHeader("Content-Type", "application/json");

  if (req.url === "/" && req.method === "GET") {
    res.statusCode = 200;
    res.end(JSON.stringify({ message: "Welcome to the API" }));
  } else if (req.url === "/users" && req.method === "GET") {
    res.statusCode = 200;
    res.end(JSON.stringify([{ id: 1, name: "Kiara" }, { id: 2, name: "Vihaan" }]));
  } else {
    res.statusCode = 404;
    res.end(JSON.stringify({ error: "Not Found" }));
  }
});

server.listen(3000, () => console.log("Server running on http://localhost:3000"));
```
**Why this is tedious (and why Module 19's Express matters):** Manually check karna `req.url`/`req.method` for every route, manually JSON stringify karna, manually status codes set karna — real applications mein bahut saare routes ke saath ye **bahut repetitive aur error-prone** ho jaata hai. Isi problem ko frameworks (jaise Express, Module 19) solve karte hain — cleaner routing syntax, middleware, aur built-in conveniences ke saath.

**Handling POST requests and request body (raw http — verbose!):**
```js
const server2 = http.createServer((req, res) => {
  if (req.method === "POST" && req.url === "/users") {
    let body = "";
    req.on("data", (chunk) => { body += chunk; }); // Streaming! (previous chapter's concept)

    req.on("end", () => {
      const userData = JSON.parse(body);
      console.log("Received:", userData);
      res.statusCode = 201;
      res.end(JSON.stringify({ message: "User created", data: userData }));
    });
  }
});
```
Notice karo — request body ko manually **stream events** (`data`, `end`) se collect karna padta hai, kyunki `req` khud ek **Readable Stream** hai (previous chapter se directly connected)! Ye raw `http` module ki verbosity dikhata hai — Express jaisi frameworks ye complexity automatically handle kar deti hain.

**Building a simple CLI tool:**
```js
#!/usr/bin/env node
// greet-cli.js

const args = process.argv.slice(2); // Module 18, chapter 03 revisited
const name = args[0] || "World";
const shout = args.includes("--shout");

let message = `Hello, ${name}!`;
if (shout) {
  message = message.toUpperCase();
}

console.log(message);

// Usage: node greet-cli.js Aditya --shout
// Output: HELLO, ADITYA!
```

**Reading user input interactively (CLI prompts):**
```js
const readline = require("readline");
const rl = readline.createInterface({ input: process.stdin, output: process.stdout });

rl.question("What's your name? ", (answer) => {
  console.log(`Nice to meet you, ${answer}!`);
  rl.close();
});
```

## 8. Mental Model

Raw `http` module ko socho **ghar khud banana, scratch se, apni ईंटो (bricks) ke saath** — poora control milta hai, lekin bahut zyada manual kaam. Frameworks (Express, Module 19) ko socho **pre-fabricated construction kit** — foundation, walls, plumbing sab already designed hain, tumhe sirf apna specific design customize karna hai.

## 9. What Happens Internally?

`http.createServer()` internally ek TCP server banata hai jo incoming network connections ko listen karta hai. Jab koi HTTP request aati hai, Node.js usse parse karta hai (headers, method, URL, body) aur `req`/`res` objects create karta hai jo **Streams** hain (`req` Readable, `res` Writable) — yahi wajah hai bade request-bodies ko chunks mein process kiya jaa sakta hai bina poora memory mein load kiye.

## 10. Common Mistakes

- `res.end()` call karna bhool jaana — response kabhi client ko nahi bheja jaayega, request "hang" ho jaayegi.
- Request body ko synchronously access karne ki koshish karna — ye stream events (`data`/`end`) ke through hi milta hai, asynchronously.
- Content-Type header set karna bhool jaana, jisse client response ko galat interpret kar sakta hai.

## 11. Edge Cases

Agar `server.listen()` ka port already kisi doosre process dwara use ho raha ho, `"EADDRINUSE"` error aata hai — common development issue jab purana server process properly close nahi hua ho.

## 12. Real-World Usage

Raw `http` module directly production mein rarely use hota hai (frameworks convenience provide karte hain), lekin **samajhna zaroori hai** kyunki ye foundation hai jis par Express/Fastify/NestJS jaisi saari frameworks bani hain. CLI tools (jaise `create-react-app`, `npm` khud) is chapter ke patterns extensively use karte hain.

## 13. Comparison With Related Concepts

| Approach | Effort | Flexibility |
|----------|----------|----------------|
| Raw `http` module | High (manual routing, parsing) | Maximum control |
| Framework (Express, etc. — Module 19) | Low (built-in conveniences) | Slightly more opinionated, but much faster development |

## 14. Practice Questions

1. `res.end()` call karna bhool jaane se kya hota hai?
2. Request body ko raw `http` module mein kaise access karte hain?
3. `process.argv` CLI tools mein kaise use hota hai?

## 15. Challenge

Ek raw `http` server banao jisme 3 routes hon: `GET /`, `GET /about`, aur ek catch-all `404` handler. Har route JSON response bheje.

## 16. Interview Questions

**Q1: Raw `http` module se ek server banane aur Express jaisi framework use karne mein kya practical difference hai?**
A: Raw `http.createServer()` **complete manual control** deta hai lekin har cheez (routing, request-body parsing, response formatting, error handling) manually implement karni padti hai — bahut saare routes ke saath ye repetitive aur error-prone ho jaata hai. Express (aur similar frameworks, Module 19 mein detail) is verbosity ko abstract kar deti hai — clean routing syntax (`app.get("/users", handler)`), automatic body-parsing middleware, aur bahut sare built-in conveniences deती hai, jisse development bahut faster aur maintainable ho jaata hai — neeche same `http` module hi kaam kar raha hota hai.

**Q2: Node.js mein HTTP request body ko access karna directly (`req.body`) kaam kyun nahi karta raw `http` module ke saath?**
A: Raw `http` module mein `req` (request) object ek **Readable Stream** hai — body data **chunks mein asynchronously** aata hai (`'data'` events ke through), turant available nahi hota jaise `req.body` property. Isliye manually `req.on('data', chunk => {...})` aur `req.on('end', () => {...})` use karke chunks ko collect aur combine karna padta hai. Frameworks jaise Express body-parsing middleware provide karte hain jo ye complexity internally handle karke seedha `req.body` property populate kar dete hain — yahi unka ek major convenience hai.

## 17. Chapter Summary

Raw `http` module se Node.js mein server banaya ja sakta hai — `createServer()`, manual routing (`req.url`/`req.method` check karke), aur stream-based request-body handling ke saath. Ye process verbose hai — frameworks (Module 19) is complexity ko abstract karte hain. CLI tools `process.argv` aur `readline` jaise built-in modules use karte hain.

## 18. Revision Checklist

- [ ] Basic raw `http` server bana sakta hoon manual routing ke saath.
- [ ] Request body ko stream-events se collect karna samajh gaya.
- [ ] Frameworks raw `http` module ke upar kya value add karte hain, clear hai.

---

**Module 18 Complete!** Next Module: [19-Backend-JavaScript/01-HTTP-Servers-and-Routing.md](../19-Backend-JavaScript/01-HTTP-Servers-and-Routing.md)
