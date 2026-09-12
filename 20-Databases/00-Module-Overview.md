# Module 20 — Databases

**Difficulty:** 🟠 Advanced
**Chapters:** 4
**Estimated time:** 6-9 hours
**Prerequisites:** [Module 19 (Backend)](../19-Backend-JavaScript/00-Module-Overview.md)

---

## Is Module Mein Kya Hai?

Tumhari API abhi data memory mein rakhti hai — server restart hote hi sab gayab. **Databases** wo permanent storage hain jahan real applications apna data rakhti hain.

Ye module sikhata hai ki SQL aur NoSQL mein kya farak hai, data store/query kaise karte hain, aur — sabse important — **database ko secure kaise rakhte hain**.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [SQL vs NoSQL](./01-SQL-vs-NoSQL.md) | Dono ka farak, kab kaunsa choose karna |
| 02 | [CRUD and Queries](./02-CRUD-and-Queries.md) | Create, Read, Update, Delete operations |
| 03 | [ORMs, Transactions, Indexing](./03-ORMs-Transactions-Indexing.md) | Prisma/Mongoose, ACID, performance |
| 04 | [**Database Security**](./04-Database-Security.md) | ⭐ SQL injection aur usse bachav |

---

## Is Module Ke Baad Tum

- [ ] Apne project ke liye sahi database choose kar paoge.
- [ ] Data ko permanently store aur query kar paoge.
- [ ] **Parameterized queries** likhoge — hamesha.
- [ ] Samajh jaoge transactions kab zaroori hain (paise/orders wale cases mein).

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **⚠️ SQL Injection — ye sabse critical point hai:**
  ```js
  // ❌ KABHI NAHI — database poora delete ho sakta hai
  db.query(`SELECT * FROM users WHERE id = ${userId}`);

  // ✅ HAMESHA — parameterized query
  db.query("SELECT * FROM users WHERE id = ?", [userId]);
  ```
  User input ko **kabhi** SQL string mein concatenate mat karo. (Chapter 04)
- **"NoSQL hamesha faster hai"** — galat. Ye use-case pe depend karta hai. Relational data (orders + users + products) ke liye SQL aksar better hai. (Chapter 01)
- **Transactions ke bina multi-step operations adhoore reh sakte hain** — paise ek account se kat gaye, dusre mein add nahi hue. (Chapter 03)
- **Indexes reads fast karte hain, writes thoda slow** — har column pe index mat lagao. (Chapter 03)

---

## Practice

- **Projects:** [E-commerce Backend](../27-Projects/Advanced/05-Ecommerce-Backend/README.md), [Full-Stack Application](../27-Projects/Advanced/06-Full-Stack-Application/README.md)
- **Security:** [Module 23](../23-Security/00-Module-Overview.md) — injection aur data exposure detail mein.

---

**Shuru karo:** [01-SQL-vs-NoSQL.md](./01-SQL-vs-NoSQL.md)
**Agla Module:** [21-Testing-and-Debugging](../21-Testing-and-Debugging/00-Module-Overview.md)
