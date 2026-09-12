# HTTP Basics

**Module:** 16-Fetch-and-APIs
**Difficulty:** 🟡 Intermediate
**Previous:** [15-Event-Loop/03-Event-Loop-Step-By-Step-Examples.md](../15-Event-Loop/03-Event-Loop-Step-By-Step-Examples.md)
**Next:** [02-Fetch-API.md](./02-Fetch-API.md)

---

## 1. Learning Objectives

- HTTP protocol ka basic concept samajhna.
- HTTP methods, headers, aur status codes ka use jaanna.
- JSON format samajhna, jo APIs mein sabse common data-format hai.

## 2. Prerequisites

Modules 01-15 complete.

## 3. Concept in Simple Hinglish

**HTTP (HyperText Transfer Protocol)** wo "language" hai jisme browser aur server baat karte hain — browser ek **request** bhejta hai ("mujhe ye data do"), server ek **response** bhejta hai ("ye lo data, ya sorry error aaya"). Har webpage load, har API call, sab HTTP requests/responses hain.

## 4. Technical Explanation

**HTTP Methods (Verbs):**

| Method | Purpose |
|--------|---------|
| `GET` | Data fetch karna (read-only, no side effects) |
| `POST` | Naya data create karna |
| `PUT` | Existing data ko poora replace karna |
| `PATCH` | Existing data ko partially update karna |
| `DELETE` | Data delete karna |

**HTTP Status Codes:**

| Range | Meaning |
|-------|---------|
| 1xx | Informational |
| 2xx | Success (200 OK, 201 Created, 204 No Content) |
| 3xx | Redirection (301 Moved, 304 Not Modified) |
| 4xx | Client Error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found) |
| 5xx | Server Error (500 Internal Server Error, 503 Service Unavailable) |

**Headers:** Metadata jo request/response ke saath jaati hai (jaise `Content-Type`, `Authorization`).

**JSON (JavaScript Object Notation):** Sabse common data-exchange format APIs mein — text-based, human-readable, JavaScript objects se directly compatible.

## 5. Syntax

```
GET /api/users/1 HTTP/1.1
Host: example.com
Authorization: Bearer <token>

Response:
HTTP/1.1 200 OK
Content-Type: application/json

{"id": 1, "name": "Aarav"}
```

## 6. Basic Examples

```js
// JSON is just a string representation of data
const jsonString = '{"name": "Diya", "age": 25}';
const obj = JSON.parse(jsonString);
console.log(obj.name); // "Diya"

const backToString = JSON.stringify(obj);
console.log(backToString); // '{"name":"Diya","age":25}'
```

## 7. Detailed Examples

**Understanding a typical REST-style URL structure:**
```
https://api.example.com/v1/users/42/orders?status=pending&limit=10
└─────┬─────┘└────┬────┘└──┬─┘└┬┘└──┬──┘└──────┬───────────────┘
  Protocol/Host   Version  Resource ID  Sub-    Query Parameters
                                        Resource
```

**Common status codes — what they mean in practice:**
```js
// 200 OK — request succeeded, here's your data
// 201 Created — a new resource was successfully created (e.g., after a POST)
// 204 No Content — success, but there's nothing to return (e.g., after a DELETE)
// 400 Bad Request — YOUR request was malformed (client's fault)
// 401 Unauthorized — you need to log in / provide valid credentials
// 403 Forbidden — you're authenticated, but not allowed to do this
// 404 Not Found — the resource doesn't exist
// 429 Too Many Requests — rate limit exceeded (Module 16, chapter 04)
// 500 Internal Server Error — something broke on the SERVER's side (not your fault)
// 503 Service Unavailable — server is temporarily overloaded/down
```

**`JSON.stringify()` — options and gotchas:**
```js
const user = { name: "Kabir", age: 28, password: "secret123", greet: function() {} };

console.log(JSON.stringify(user));
// '{"name":"Kabir","age":28,"password":"secret123"}' — functions are SILENTLY dropped!

console.log(JSON.stringify(user, null, 2)); // Pretty-print with 2-space indentation
/*
{
  "name": "Kabir",
  "age": 28,
  "password": "secret123"
}
*/

console.log(JSON.stringify(user, ["name", "age"])); // Only include specified keys (a "replacer" array)
// '{"name":"Kabir","age":28}'
```

**`JSON.parse()` — handling invalid JSON:**
```js
try {
  const data = JSON.parse("{invalid json}");
} catch (error) {
  console.log("Parsing failed:", error.message); // "Unexpected token i in JSON..."
}
```
API responses parse karte waqt `try/catch` important hai — agar server ne galti se HTML error page bheja (JSON ke bajaye), `JSON.parse()` throw karega.

**Headers — a common example (Content-Type and Authorization):**
```js
// When SENDING data to a server, you tell it what format you're sending:
// Content-Type: application/json

// When AUTHENTICATING, you typically send a token:
// Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

## 8. Mental Model

HTTP request/response ko socho ek **restaurant order** ki tarah — tum (client) ek order (request) dete ho jisme specific instructions (method, headers) hoti hain, kitchen (server) usse process karta hai aur ek response (order ready/error) bhejta hai, jisme "status code" (order fulfilled? problem hui?) aur actual food (data/body) hota hai.

## 9. What Happens Internally?

Jab browser koi HTTP request bhejta hai, ye network layer ke through TCP/IP connection banata hai server tak, request ko formatted text (headers + body) ki tarah bhejta hai, aur server se response ka wait karta hai — ye sab process JavaScript ke `fetch()` API (next chapter) ke through abstract kiya jaata hai, taaki developer ko low-level networking details handle na karni pade.

## 10. Common Mistakes

- Status code categories ko confuse karna (jaise 4xx client error ko 5xx server error samajhna).
- `JSON.stringify()` mein functions/undefined values silently drop hone ka pata na hona.
- `JSON.parse()` ko bina `try/catch` ke use karna jab response invalid JSON ho sakta hai.

## 11. Edge Cases

```js
console.log(JSON.stringify(undefined));  // undefined (not a string!)
console.log(JSON.stringify(null));         // "null"
console.log(JSON.stringify([undefined, function(){}, Symbol()])); // '[null,null,null]' — array preserves positions differently than objects!
```

## 12. Real-World Usage

Har frontend application backend APIs se HTTP ke through communicate karta hai. Status codes error-handling logic drive karte hain (jaise 401 pe login page pe redirect karna). JSON format almost universal hai REST APIs mein.

## 13. Comparison With Related Concepts

| Method | Typical Use |
|--------|----------------|
| GET | Fetch data (safe, no side effects, cacheable) |
| POST | Create new data (not idempotent) |
| PUT/PATCH | Update data (PUT = full replace, PATCH = partial) |
| DELETE | Remove data |

## 14. Practice Questions

1. 404 aur 500 status codes mein kya difference hai (client-fault vs server-fault)?
2. `PUT` aur `PATCH` mein kya difference hai?
3. `JSON.stringify()` functions ko kaise handle karta hai?

## 15. Challenge

Ek object banao jisme string, number, boolean, nested object, aur ek function property ho. `JSON.stringify()` se convert karo aur observe karo kya included/excluded hota hai.

## 16. Interview Questions

**Q1: HTTP status codes ki categories kya hain aur unka general matlab kya hai?**
A: Status codes 5 categories mein bante hain: **1xx** (Informational, rare use), **2xx** (Success — jaise 200 OK, 201 Created), **3xx** (Redirection — jaise 301 Moved Permanently), **4xx** (Client Error — client ne kuch galat kiya, jaise 400 Bad Request, 404 Not Found), aur **5xx** (Server Error — server side pe problem hui, client ka fault nahi, jaise 500 Internal Server Error). Ye categorization error-handling logic likhne mein help karti hai — jaise 4xx pe user ko batana "apna input check karo", 5xx pe "kuch der baad try karo".

**Q2: `PUT` aur `PATCH` HTTP methods mein kya difference hai?**
A: `PUT` ek resource ko **completely replace** karta hai — agar tum sirf ek field bhejte ho, baaki fields potentially "lost"/reset ho sakti hain (server implementation pe depend karta hai, but conventionally full replacement expected hai). `PATCH` ek resource ko **partially update** karta hai — sirf diye gaye fields change hoti hain, baaki untouched rehti hain. General convention: agar tumhe sirf ek field update karni hai (jaise sirf email change karna), `PATCH` zyada appropriate hai `PUT` se.

## 17. Chapter Summary

HTTP client-server communication ka protocol hai — methods (GET/POST/PUT/PATCH/DELETE) action define karte hain, status codes (2xx/3xx/4xx/5xx) outcome batate hain, headers metadata carry karte hain. JSON sabse common data format hai, `JSON.stringify()`/`JSON.parse()` se JavaScript objects ke saath convert hota hai.

## 18. Revision Checklist

- [ ] HTTP methods aur unka correct use-case yaad hai.
- [ ] Status code categories (2xx/4xx/5xx) clear hain.
- [ ] `JSON.stringify()`/`JSON.parse()` ka behavior aur gotchas samajh gaye.

---

**Next:** [02-Fetch-API.md](./02-Fetch-API.md)
