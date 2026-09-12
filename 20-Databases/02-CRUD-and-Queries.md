# CRUD and Queries (Database Level)

**Module:** 20-Databases
**Difficulty:** 🟠 Advanced
**Previous:** [01-SQL-vs-NoSQL.md](./01-SQL-vs-NoSQL.md)
**Next:** [03-ORMs-Transactions-Indexing.md](./03-ORMs-Transactions-Indexing.md)

---

## 1. Learning Objectives

- Basic SQL queries likhna (SELECT, INSERT, UPDATE, DELETE).
- MongoDB-style NoSQL queries likhna.
- Database connections ko application code se connect karna (conceptually).

## 2. Prerequisites

[01-SQL-vs-NoSQL.md](./01-SQL-vs-NoSQL.md). Module 16, chapter 03 (REST/CRUD) is chapter ka natural extension hai — application-level CRUD (API endpoints) ke peeche database-level CRUD yahi hai.

## 3. Concept in Simple Hinglish

Module 16 mein humne dekha tha REST APIs CRUD (Create, Read, Update, Delete) operations ko HTTP methods se map karte hain. Ye operations **actually database ke level pe queries ban jaate hain** — is chapter mein hum dekhenge ki wo queries kaisi dikhti hain SQL aur NoSQL dono mein.

## 4. Technical Explanation

**SQL CRUD Commands:** `SELECT` (Read), `INSERT` (Create), `UPDATE` (Update), `DELETE` (Delete).

**MongoDB CRUD Methods:** `find()`/`findOne()` (Read), `insertOne()`/`insertMany()` (Create), `updateOne()`/`updateMany()` (Update), `deleteOne()`/`deleteMany()` (Delete).

## 5. Syntax

```sql
SELECT * FROM table_name WHERE condition;
INSERT INTO table_name (col1, col2) VALUES (val1, val2);
UPDATE table_name SET col1 = val1 WHERE condition;
DELETE FROM table_name WHERE condition;
```

```js
collection.find({ field: value });
collection.insertOne({ field: value });
collection.updateOne({ filter }, { $set: { field: newValue } });
collection.deleteOne({ filter });
```

## 6. Basic Examples

```sql
SELECT name, email FROM users WHERE age > 18;
INSERT INTO users (name, email, age) VALUES ('Ira', 'ira@email.com', 24);
UPDATE users SET age = 25 WHERE name = 'Ira';
DELETE FROM users WHERE name = 'Ira';
```

## 7. Detailed Examples

**SQL — a complete CRUD walkthrough:**
```sql
-- CREATE (Insert)
INSERT INTO products (name, price, category)
VALUES ('Wireless Mouse', 799, 'Electronics');

-- READ (Select) — with filtering, sorting, and limiting
SELECT * FROM products WHERE category = 'Electronics' ORDER BY price DESC LIMIT 10;

-- READ — with a JOIN (connecting related tables, Module 20 chapter 01 concept)
SELECT orders.id, users.name, orders.amount
FROM orders
JOIN users ON orders.user_id = users.id
WHERE orders.amount > 1000;

-- UPDATE
UPDATE products SET price = 699 WHERE name = 'Wireless Mouse';

-- DELETE
DELETE FROM products WHERE id = 5;
```

**MongoDB (NoSQL) — the equivalent operations:**
```js
// CREATE
db.products.insertOne({ name: "Wireless Mouse", price: 799, category: "Electronics" });

// READ — with filtering, sorting, and limiting
db.products.find({ category: "Electronics" }).sort({ price: -1 }).limit(10);

// READ — using MongoDB's aggregation for "join-like" operations (more complex, called $lookup)
db.orders.aggregate([
  { $lookup: { from: "users", localField: "userId", foreignField: "_id", as: "user" } },
  { $match: { amount: { $gt: 1000 } } }
]);

// UPDATE
db.products.updateOne({ name: "Wireless Mouse" }, { $set: { price: 699 } });

// DELETE
db.products.deleteOne({ _id: 5 });
```

**Using a database from Node.js — the general pattern (connecting to Module 19's Express APIs):**
```js
// Example using a hypothetical/simplified database client (concept applies broadly)
const express = require("express");
const app = express();
app.use(express.json());

// Assume 'db' is an established database connection (setup varies by database/driver)

app.get("/products", async (req, res) => {
  try {
    const products = await db.query("SELECT * FROM products"); // Or db.collection('products').find()
    res.json(products);
  } catch (error) {
    res.status(500).json({ error: "Database error" });
  }
});

app.post("/products", async (req, res) => {
  const { name, price, category } = req.body;
  try {
    const result = await db.query(
      "INSERT INTO products (name, price, category) VALUES ($1, $2, $3) RETURNING *",
      [name, price, category] // Parameterized query — see Module 23 for WHY this matters (SQL injection)
    );
    res.status(201).json(result[0]);
  } catch (error) {
    res.status(500).json({ error: "Database error" });
  }
});
```
**⚠️ Critical security note (preview of Module 23):** Query mein `[name, price, category]` ko **parameters** ki tarah pass kiya gaya hai (`$1, $2, $3`), string-concatenation se nahi (`"INSERT INTO products VALUES ('" + name + "', ...)"`) — ye SQL Injection attacks se bachne ka essential pattern hai, jo Module 23 mein deeply cover hoga.

## 8. Mental Model

SQL queries ko socho **precise, structured instructions ek librarian ko** ("mujhe fiction section ki wo saari books do jo 2020 ke baad publish hui, price ke hisaab se sorted") — bahut specific aur structured. MongoDB queries ko socho **JavaScript-object-jaisi filters** — jo tumhe already familiar lagengi kyunki syntax JavaScript objects se milta-julta hai.

## 9. What Happens Internally?

Database engine query ko parse karta hai, ek **execution plan** banata hai (kaunse indexes use karne hain, kaunsa order best hai), aur data ko disk/memory se retrieve/modify karta hai. Modern databases query-optimization ke liye sophisticated algorithms use karte hain — same query different execution plans le sakti hai based on data-size, indexes (next chapter).

## 10. Common Mistakes

- User input ko directly SQL query mein string-concatenate karna — SQL Injection vulnerability (Module 23 mein critical detail).
- `DELETE`/`UPDATE` queries `WHERE` clause ke bina chalana — **saari** rows affect ho jaati hain accidentally.
- Bahut saara data ek saath fetch karna (`SELECT *` bina `LIMIT` ke) jab pagination (Module 16, chapter 04) zaroori thi.

## 11. Edge Cases

```sql
-- ⚠️ DANGEROUS — no WHERE clause means ALL rows get deleted!
-- DELETE FROM users;

-- ✅ Always double-check WHERE clauses before running DELETE/UPDATE in production!
DELETE FROM users WHERE id = 42;
```

## 12. Real-World Usage

Har backend application jo persistent data store karti hai (users, products, orders, posts) database CRUD operations use karti hai — ye Module 19 ke Express routes ka "next step" hai (route handler → database query → response).

## 13. Comparison With Related Concepts

| Operation | SQL | MongoDB |
|-----------|-----|---------|
| Create | `INSERT INTO` | `insertOne()`/`insertMany()` |
| Read | `SELECT` | `find()`/`findOne()` |
| Update | `UPDATE ... SET` | `updateOne()`/`updateMany()` |
| Delete | `DELETE FROM` | `deleteOne()`/`deleteMany()` |

## 14. Practice Questions

1. `WHERE` clause ke bina `DELETE` query chalane se kya risk hai?
2. Parameterized queries (`$1, $2`) string-concatenation se safer kyun hain?
3. MongoDB mein `find()` aur SQL mein `SELECT` conceptually kaise similar hain?

## 15. Challenge

Ek `orders` table/collection design karo (conceptually) jisme `id`, `userId`, `amount`, `status` fields hon. SQL aur MongoDB-style dono mein queries likho: (1) sabhi "pending" orders fetch karo, (2) ek order ka status "completed" mein update karo.

## 16. Interview Questions

**Q1: Parameterized queries (jaise `$1, $2` placeholders) directly string-concatenation se query banane se better kyun hain?**
A: Parameterized queries user-input ko query ke **structure** se completely separate rakhte hain — database driver values ko safely "escape" karke handle karta hai, taaki wo kabhi bhi accidentally SQL command ki tarah interpret na ho. String concatenation (`"SELECT * FROM users WHERE name = '" + userInput + "'"`) allow karta hai ki agar `userInput` mein malicious SQL code ho (jaise `'; DROP TABLE users; --`), wo actually query ka part ban jaaye — ye **SQL Injection** attack hai (Module 23 mein deeply cover hoga), jo application ki poori database ko compromise kar sakta hai.

**Q2: `DELETE`/`UPDATE` queries mein `WHERE` clause bhool jaane se kya hota hai?**
A: Bina `WHERE` clause ke, `DELETE FROM table_name` **table ki saari rows delete kar dega**, aur `UPDATE table_name SET column = value` **saari rows ko update kar dega** — ye ek catastrophic, often irreversible mistake hai production databases mein. Isi liye best practice hai `DELETE`/`UPDATE` likhte waqt hamesha pehle `SELECT` ke saath same `WHERE` clause test karna (ye confirm karne ke liye ki sahi rows affected hongi), phir hi actual `DELETE`/`UPDATE` run karna.

## 17. Chapter Summary

CRUD operations SQL mein `SELECT`/`INSERT`/`UPDATE`/`DELETE` commands ban jaate hain, MongoDB mein `find`/`insertOne`/`updateOne`/`deleteOne` methods. Parameterized queries user-input ko safely handle karte hain, SQL injection avoid karte hain. `WHERE` clause ke bina destructive operations poori table ko affect kar sakte hain — extreme caution zaroori hai.

## 18. Revision Checklist

- [ ] Basic SQL CRUD syntax likh sakta hoon.
- [ ] MongoDB-style CRUD syntax ka basic idea hai.
- [ ] Parameterized queries ki security-importance clear hai.

---

**Next:** [03-ORMs-Transactions-Indexing.md](./03-ORMs-Transactions-Indexing.md)
