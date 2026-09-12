# Fetch API

**Module:** 16-Fetch-and-APIs
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [01-HTTP-Basics.md](./01-HTTP-Basics.md)
**Next:** [03-REST-and-CRUD.md](./03-REST-and-CRUD.md)

---

## 1. Learning Objectives

- `fetch()` se GET aur POST requests bhejna.
- Fetch response object ko correctly handle karna.
- Fetch ke error-handling ki "silent failure" trap samajhna.

## 2. Prerequisites

Module 16, chapter 01. Module 13-14 (Promises, async/await).

## 3. Concept in Simple Hinglish

`fetch()` JavaScript ka built-in tareeka hai HTTP requests bhejne ka — server se data lena ya bhejna. Ye **Promise-based** hai (Module 13 se yaad karo), isliye `.then()` ya `async`/`await` ke saath naturally kaam karta hai.

## 4. Technical Explanation

`fetch(url, options)` ek Promise return karta hai jo **Response object** se resolve hota hai — lekin important: ye Promise sirf reject hota hai **network failure** pe (jaise no internet), **HTTP error status codes (4xx, 5xx) pe reject NAHI hota** — ye ek critical gotcha hai.

Response object ke useful methods: `.json()`, `.text()`, `.blob()` — sab **khud bhi Promises return karte hain** (body ko async read karte hain).

## 5. Syntax

```js
fetch(url)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.log(error));

// With async/await
async function getData() {
  const response = await fetch(url);
  const data = await response.json();
  return data;
}
```

## 6. Basic Examples

```js
fetch("https://api.example.com/users/1")
  .then(response => response.json())
  .then(user => console.log(user));
```

## 7. Detailed Examples

**⚠️ The critical gotcha — fetch does NOT reject on HTTP errors:**
```js
fetch("https://api.example.com/nonexistent-endpoint") // Returns a 404
  .then(response => {
    console.log(response.ok);      // false
    console.log(response.status);   // 404
    return response.json();          // This might still "succeed" if the server sends a JSON error body!
  })
  .then(data => console.log(data)); // This runs — .catch() is NOT triggered by a 404!
```
`fetch()` sirf **network-level failures** (DNS error, no connection, CORS block) pe reject hota hai. Ek 404 ya 500 response bhi ek "successful fetch" mana jaata hai (Promise resolve hoti hai) — isliye **manually `response.ok` ya `response.status` check karna zaroori hai**.

**✅ Correct pattern — manually checking for HTTP errors:**
```js
async function fetchUser(id) {
  const response = await fetch(`https://api.example.com/users/${id}`);

  if (!response.ok) { // response.ok is true only for status codes 200-299
    throw new Error(`HTTP error! Status: ${response.status}`);
  }

  return response.json();
}

async function displayUser() {
  try {
    const user = await fetchUser(999); // Assume this doesn't exist (404)
    console.log(user);
  } catch (error) {
    console.log("Failed to fetch user:", error.message); // NOW this catches it correctly!
  }
}
```

**Sending data — POST request with a JSON body:**
```js
async function createUser(userData) {
  const response = await fetch("https://api.example.com/users", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(userData) // Must stringify — fetch doesn't do this automatically!
  });

  if (!response.ok) {
    throw new Error(`Failed to create user: ${response.status}`);
  }

  return response.json();
}

createUser({ name: "Nisha", email: "nisha@example.com" })
  .then(newUser => console.log("Created:", newUser))
  .catch(error => console.log(error.message));
```

**Adding authentication headers:**
```js
async function fetchProtectedData(token) {
  const response = await fetch("https://api.example.com/protected", {
    headers: {
      "Authorization": `Bearer ${token}`
    }
  });
  if (!response.ok) throw new Error("Unauthorized or request failed");
  return response.json();
}
```

**Handling network errors vs HTTP errors — both cases together:**
```js
async function robustFetch(url) {
  try {
    const response = await fetch(url); // Might throw for NETWORK errors (no internet, CORS, etc.)
    if (!response.ok) {
      throw new Error(`Server responded with status ${response.status}`); // Manual check for HTTP errors
    }
    return await response.json();
  } catch (error) {
    // This catches BOTH network errors AND our manually-thrown HTTP errors
    console.log("Request failed:", error.message);
    throw error; // Re-throw so the caller can also handle it if needed
  }
}
```

## 8. Mental Model

`fetch()` ko socho ek **postman jo sirf "kya chitthi deliver ho payi ya nahi"** ye guarantee karta hai — agar ghar mila (server responded), postman apna kaam successfully complete mana jaata hai, chahe chitthi mein "sorry, no vacancy" (404) hi kyun na likha ho. Postman ye check nahi karta chitthi ka content kya hai — wo tumhara kaam hai (`response.ok` check karna).

## 9. What Happens Internally?

`fetch()` browser ke networking layer ko HTTP request handoff karta hai (Module 15 - Event Loop se connected — network operation Web APIs mein hoti hai). Jab response headers aa jaate hain, `fetch()` ka Promise resolve ho jaata hai — lekin **body abhi tak fully download nahi hui hoti** (large responses ke liye), isi liye `.json()`/`.text()` alag se async operations hain jo body ko stream karke read karte hain.

## 10. Common Mistakes

- `response.ok` check na karna aur HTTP errors ko silently ignore kar dena.
- `body: JSON.stringify(data)` bhool jaana aur raw object bhejne ki koshish karna (jo error dega — body string/Blob/etc honi chahiye).
- `Content-Type: application/json` header bhool jaana POST requests mein.

## 11. Edge Cases

```js
fetch("https://invalid-domain-that-does-not-exist-xyz123.com")
  .catch(error => console.log(error.message)); // "Failed to fetch" — THIS is a network error, correctly caught
```

## 12. Real-World Usage

`fetch()` almost har modern frontend application mein backend APIs se data lene ke liye use hota hai — user lists, product catalogs, form submissions, authentication, sab jagah. Libraries jaise Axios `fetch()` ke upar convenience layer provide karti hain (automatic JSON parsing, automatic error-throwing on bad status codes) — inka use bhi common hai.

## 13. Comparison With Related Concepts

| Aspect | `fetch()` | Axios (popular alternative library) |
|--------|-------------|------------------------------------------|
| Built-in? | ✅ Yes, native | ❌ No, needs installation |
| Rejects on HTTP error (4xx/5xx)? | ❌ No (manual check needed) | ✅ Yes, automatically |
| Auto JSON parsing | ❌ No (`.json()` needed) | ✅ Yes, automatic |

## 14. Practice Questions

1. `fetch()` HTTP 404 response pe reject hota hai ya resolve?
2. POST request mein body bhejne se pehle kya karna zaroori hai?
3. `response.ok` kya check karta hai?

## 15. Challenge

Ek function `safeFetch(url)` likho jo `fetch()` use kare, `response.ok` check kare, aur agar error ho to meaningful error message ke saath throw kare. Isse `https://jsonplaceholder.typicode.com/users/1` (valid) aur `https://jsonplaceholder.typicode.com/invalid` (invalid) dono ke saath test karo.

## 16. Interview Questions

**Q1: `fetch()` HTTP error status codes (jaise 404, 500) pe apna Promise reject kyun nahi karta?**
A: `fetch()` API ka design decision hai ki wo sirf **network-level failures** (jaise connection na ban paana, DNS resolve na hona, CORS block) ko "failure" maanta hai. Ek server se koi bhi response mil jaana (chahe 200 ho ya 404 ho) `fetch()` ke liye "successful communication" hai — matlab server se baat ho paayi. Isliye developer ko manually `response.ok` (ya `response.status`) check karke decide karna padta hai ki request "business logic" ke hisaab se successful thi ya nahi, aur agar zaroorat ho to khud explicitly error throw karni padti hai.

**Q2: `fetch()` se POST request bhejte waqt kaunse common steps miss ho sakte hain?**
A: Do common mistakes hain: (1) `body` ke liye object ko `JSON.stringify()` se convert karna bhool jaana — `fetch()` automatically object ko JSON string mein convert nahi karta, raw object bhejne se error aata hai. (2) `Content-Type: application/json` header set karna bhool jaana — bina isके, server request body ko sahi se interpret nahi kar payega ki ye JSON data hai.

## 17. Chapter Summary

`fetch()` Promise-based HTTP requests API hai. Critical gotcha: ye sirf network failures pe reject hota hai, HTTP error status codes pe nahi — `response.ok` manually check karna zaroori hai. POST requests ke liye `JSON.stringify()` aur `Content-Type` header zaroori hain.

## 18. Revision Checklist

- [ ] `fetch()` ka "resolves even on 404/500" gotcha yaad hai.
- [ ] `response.ok` check pattern likh sakta hoon.
- [ ] POST request ka complete syntax (method, headers, body) yaad hai.

---

**Next:** [03-REST-and-CRUD.md](./03-REST-and-CRUD.md)
