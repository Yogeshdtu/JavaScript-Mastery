# REST and CRUD

**Module:** 16-Fetch-and-APIs
**Difficulty:** 🟡 Intermediate
**Previous:** [02-Fetch-API.md](./02-Fetch-API.md)
**Next:** [04-Auth-CORS-RateLimiting-Pagination.md](./04-Auth-CORS-RateLimiting-Pagination.md)

---

## 1. Learning Objectives

- REST architecture ke principles samajhna.
- CRUD operations ko REST endpoints se map karna.
- Real API interaction patterns likhna (Fetch API ke saath combine karke).

## 2. Prerequisites

Module 16, chapters 01-02.

## 3. Concept in Simple Hinglish

**REST (Representational State Transfer)** ek "convention/style" hai APIs design karne ka — jisme har "resource" (jaise users, products, orders) ka apna URL hota hai, aur HTTP methods (GET/POST/PUT/DELETE) decide karte hain ki us resource pe **kya action** perform karna hai. **CRUD** (Create, Read, Update, Delete) wo 4 basic operations hain jo almost har data-driven application karti hai.

## 4. Technical Explanation

**CRUD to HTTP mapping:**

| CRUD Operation | HTTP Method | Example Endpoint |
|-----------------|----------------|------------------------|
| Create | `POST` | `POST /users` |
| Read (all) | `GET` | `GET /users` |
| Read (one) | `GET` | `GET /users/5` |
| Update | `PUT`/`PATCH` | `PUT /users/5` |
| Delete | `DELETE` | `DELETE /users/5` |

**REST Principles:**
- **Resource-based URLs:** URLs "nouns" hote hain (`/users`), verbs nahi (`/getUsers` ❌).
- **Stateless:** Har request apne aap mein complete hoti hai — server previous requests "yaad" nahi rakhta.
- **Standard HTTP methods:** Actions HTTP methods se represent hote hain, URL se nahi.

## 5. Syntax

```
GET    /api/products          → List all products
GET    /api/products/42        → Get product with ID 42
POST   /api/products           → Create a new product
PUT    /api/products/42        → Replace product 42 entirely
PATCH  /api/products/42        → Partially update product 42
DELETE /api/products/42        → Delete product 42
```

## 6. Basic Examples

```js
// READ (all)
fetch("https://api.example.com/products")
  .then(res => res.json())
  .then(products => console.log(products));
```

## 7. Detailed Examples

**Complete CRUD implementation with Fetch API:**
```js
const BASE_URL = "https://api.example.com/products";

// CREATE
async function createProduct(product) {
  const response = await fetch(BASE_URL, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(product)
  });
  if (!response.ok) throw new Error("Failed to create product");
  return response.json();
}

// READ (all)
async function getAllProducts() {
  const response = await fetch(BASE_URL);
  if (!response.ok) throw new Error("Failed to fetch products");
  return response.json();
}

// READ (one)
async function getProduct(id) {
  const response = await fetch(`${BASE_URL}/${id}`);
  if (!response.ok) throw new Error(`Product ${id} not found`);
  return response.json();
}

// UPDATE (partial)
async function updateProduct(id, changes) {
  const response = await fetch(`${BASE_URL}/${id}`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(changes)
  });
  if (!response.ok) throw new Error("Failed to update product");
  return response.json();
}

// DELETE
async function deleteProduct(id) {
  const response = await fetch(`${BASE_URL}/${id}`, { method: "DELETE" });
  if (!response.ok) throw new Error("Failed to delete product");
  return response.status === 204 ? null : response.json(); // 204 = No Content
}
```

**Using these functions together — a realistic workflow:**
```js
async function manageInventory() {
  try {
    const newProduct = await createProduct({ name: "Laptop", price: 55000 });
    console.log("Created:", newProduct);

    const updated = await updateProduct(newProduct.id, { price: 50000 });
    console.log("Updated:", updated);

    await deleteProduct(newProduct.id);
    console.log("Deleted successfully");
  } catch (error) {
    console.log("Inventory operation failed:", error.message);
  }
}
```

**Nested resources — REST convention for relationships:**
```
GET /users/5/orders          → All orders belonging to user 5
GET /users/5/orders/12        → Specific order 12 belonging to user 5
POST /users/5/orders           → Create a new order for user 5
```

## 8. Mental Model

REST APIs ko socho ek **well-organized library** — har book (resource) ka ek unique shelf-location (URL) hai. "Verb" (kya karna hai — issue karna, return karna, renew karna) alag se specify hota hai (HTTP method), book ka naam URL mein bhar-bhar ke nahi likhna padta.

## 9. What Happens Internally?

Jab REST API request receive karta hai, server URL (route) aur HTTP method ko parse karta hai, appropriate handler function ko route karta hai (Module 19 mein "routing" concept detail se cover hoga), database ke saath interact karta hai (Module 20), aur ek response (usually JSON) generate karke bhejta hai.

## 10. Common Mistakes

- URLs mein verbs use karna (`/getUsers`, `/deleteProduct/5`) jab RESTful convention resource-nouns + HTTP-methods use karta hai.
- `PUT` aur `PATCH` ko interchangeably use karna jab semantic difference matter karta hai.
- Nested resource URLs ko bahut deep bana dena (jaise `/users/5/orders/12/items/3/reviews/1`) — usually 2 levels tak limit rakhna best practice hai.

## 11. Edge Cases

Kuch APIs strictly REST follow nahi karte (jaise GraphQL — ek alternative query-based approach, jo is course ke scope se bahar hai but mention karna zaroori hai ki REST hi sirf approach nahi hai).

## 12. Real-World Usage

Almost saari modern web/mobile applications backend se REST APIs (ya similar conventions) ke through communicate karti hain — e-commerce (products, orders, carts), social media (posts, comments, likes), banking (accounts, transactions) sab RESTful patterns follow karte hain.

## 13. Comparison With Related Concepts

| Term | Relation |
|------|----------|
| REST | Architectural style/convention for API design |
| CRUD | The 4 basic data operations, mapped to REST via HTTP methods |
| Fetch API | The JavaScript tool used to actually make these REST calls |

## 14. Practice Questions

1. CRUD ke 4 operations REST mein kaunse HTTP methods se map hote hain?
2. `POST /users` aur `GET /users` mein kya difference hai (same URL, different method)?
3. Nested resource URL ka example likho (jaise ek user ke orders).

## 15. Challenge

Ek `TaskManager` object banao jisme 4 methods ho (`createTask`, `getTasks`, `updateTask`, `deleteTask`) jo fictional REST API (`https://api.example.com/tasks`) ke saath CRUD operations perform karein, Fetch API use karke.

## 16. Interview Questions

**Q1: REST kya hai aur RESTful API design ke basic principles kya hain?**
A: REST (Representational State Transfer) ek architectural style hai web APIs design karne ke liye. Core principles hain: (1) **Resource-based URLs** — URLs "nouns" represent karte hain (jaise `/users`), actions nahi; (2) **Standard HTTP methods** decide karte hain action kya hai (GET=read, POST=create, PUT/PATCH=update, DELETE=remove); (3) **Statelessness** — har request independent hoti hai, server client ki "memory" nahi rakhta requests ke beech; (4) Responses usually **JSON** format mein hote hain.

**Q2: CRUD operations REST APIs mein kaise implement hote hain?**
A: CRUD (Create, Read, Update, Delete) ke 4 operations specific HTTP methods se map hote hain ek resource ke URL pe: **Create** = `POST` (jaise `POST /products`), **Read** = `GET` (`GET /products` for all, `GET /products/5` for one specific item), **Update** = `PUT` (full replace) ya `PATCH` (partial update) on `/products/5`, **Delete** = `DELETE` on `/products/5`. Same base URL (`/products`), different HTTP method — yahi REST ka core idea hai.

## 17. Chapter Summary

REST APIs resources ko URLs se represent karte hain aur actions ko HTTP methods se — ye consistent, predictable API design deta hai. CRUD ke 4 operations (Create/Read/Update/Delete) directly HTTP methods (POST/GET/PUT-PATCH/DELETE) se map hote hain. Fetch API in patterns ko JavaScript se implement karne ka tool hai.

## 18. Revision Checklist

- [ ] CRUD-to-HTTP-method mapping yaad hai.
- [ ] RESTful URL conventions (nouns not verbs) clear hain.
- [ ] Complete CRUD functions Fetch API ke saath likh sakta hoon.

---

**Next:** [04-Auth-CORS-RateLimiting-Pagination.md](./04-Auth-CORS-RateLimiting-Pagination.md)
