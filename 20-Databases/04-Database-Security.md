# Database Security

**Module:** 20-Databases
**Difficulty:** 🔴 Advanced
**Previous:** [03-ORMs-Transactions-Indexing.md](./03-ORMs-Transactions-Indexing.md)
**Next:** [21-Testing-and-Debugging/01-Testing-Fundamentals.md](../21-Testing-and-Debugging/01-Testing-Fundamentals.md)

---

## 1. Learning Objectives

- SQL Injection ka mechanism aur prevention samajhna (Module 20, chapter 02 se aage badhate hue).
- Connection pooling ka concept samajhna.
- Sensitive data handling best practices jaanna.

## 2. Prerequisites

Module 20, chapters 01-03.

## 3. Concept in Simple Hinglish

Database production application ka **sabse critical asset** hai — usme users ka personal data, passwords (hashed), financial information hoti hai. Is chapter mein hum database ko secure rakhne ke essential practices dekhenge — full security detail Module 23 mein aayega, ye database-specific concerns pe focus karta hai.

## 4. Technical Explanation

**SQL Injection:** Jab user-input directly SQL query mein concatenate kiya jaata hai, attacker malicious SQL code inject kar sakta hai jo query ka behavior badal deta hai.

**Connection Pooling:** Har database-operation ke liye naya connection banane ke bajaye, ek "pool" of reusable connections maintain karna — performance ke liye critical.

**Principle of Least Privilege:** Database users/applications ko sirf **utni hi permissions** deni chahiye jitni zaroorat hai — sab kuch "admin access" nahi.

## 5. Syntax

```js
// ⚠️ Vulnerable (string concatenation)
const query = `SELECT * FROM users WHERE email = '${userInput}'`;

// ✅ Safe (parameterized query)
const query = "SELECT * FROM users WHERE email = $1";
db.query(query, [userInput]);
```

## 6. Basic Examples

```js
// ⚠️ NEVER do this
app.get("/user", (req, res) => {
  const query = `SELECT * FROM users WHERE name = '${req.query.name}'`;
  db.query(query); // VULNERABLE!
});
```

## 7. Detailed Examples

**SQL Injection — seeing the actual attack (educational, to understand the risk):**
```js
// ⚠️ Vulnerable code
const userInput = req.query.name;
const query = `SELECT * FROM users WHERE name = '${userInput}'`;

// If an attacker provides this as userInput:
//   ' OR '1'='1

// The query becomes:
//   SELECT * FROM users WHERE name = '' OR '1'='1'
// '1'='1' is ALWAYS true — this returns ALL users, bypassing any intended filtering!

// An even more dangerous input:
//   '; DROP TABLE users; --

// The query becomes:
//   SELECT * FROM users WHERE name = ''; DROP TABLE users; --'
// This could DELETE THE ENTIRE TABLE, depending on database/driver configuration!
```
**✅ The fix — parameterized queries (revisited from Module 20, chapter 02):**
```js
const query = "SELECT * FROM users WHERE name = $1";
db.query(query, [userInput]);
// Even if userInput is "'; DROP TABLE users; --", it's treated as a LITERAL STRING VALUE
// to search for, never as executable SQL code. The attack is completely neutralized.
```
**Key principle:** User input **kabhi** query-structure ka part nahi banna chahiye — sirf **data** ki tarah treat hona chahiye. ORMs (previous chapter) automatically ye protection provide karte hain internally.

**Connection pooling — why it matters for performance:**
```js
// ⚠️ Inefficient — opening/closing a connection for EVERY query is expensive
async function getUserInefficient(id) {
  const connection = await createNewConnection(); // Slow — network handshake, auth, etc.
  const result = await connection.query("SELECT * FROM users WHERE id = $1", [id]);
  await connection.close();
  return result;
}

// ✅ Efficient — reuse connections from a pool
const pool = createConnectionPool({ max: 10 }); // Maintain up to 10 ready-to-use connections

async function getUserEfficient(id) {
  const result = await pool.query("SELECT * FROM users WHERE id = $1", [id]); // Reuses an existing connection!
  return result;
}
```
Connection pooling especially important hai high-traffic applications mein — bahut saare simultaneous requests ke liye har baar naya connection banana bahut resource-expensive hota.

**Sensitive data handling — practical checklist:**
```js
// ⚠️ Never return password hashes to the client, even accidentally!
app.get("/user/:id", async (req, res) => {
  const user = await User.findByPk(req.params.id);
  res.json(user); // ⚠️ This might include the hashed password field!
});

// ✅ Explicitly exclude sensitive fields
app.get("/user/:id", async (req, res) => {
  const user = await User.findByPk(req.params.id, {
    attributes: { exclude: ["password"] } // ORM-level field exclusion
  });
  res.json(user);
});

// ✅ Alternative — manually destructure to only include safe fields
function toSafeUser(user) {
  const { password, ...safeUser } = user; // Module 07, chapter 02 (rest in destructuring)!
  return safeUser;
}
```

## 8. Mental Model

SQL Injection ko socho ek **note jo tum kisi ko dete ho, aur wo note khud ek "naya instruction" ban jaata hai** jo original message se bahar chala jaata hai — jaise agar tum kisi ko delivery-address likh ke doge, aur wo address khud mein ek "also, give them all my money" wala instruction chhupa hua ho. Parameterized queries ensure karte hain ki delivery-address hamesha sirf address ki tarah treat ho, kabhi instruction ki tarah nahi. Connection pooling ko socho ek **taxi stand jaha taxis ready khadi hain** — har baar naya taxi manufacture karne ke bajaye, existing taxis reuse hoti hain.

## 9. What Happens Internally?

Parameterized queries database driver ko values ko **query-structure se separately** bhejne dete hain — database engine unhe literal data ki tarah treat karta hai, chahe unme special characters (`'`, `;`, `--`) ho jo normally SQL syntax mein special meaning rakhte hain. Connection pools internally ek queue of open connections maintain karte hain, requests ko available connections assign karte hain aur unhe operation complete hone ke baad wapas pool mein return kar dete hain (close nahi karte).

## 10. Common Mistakes

- User input ko kisi bhi form mein directly query-string mein concatenate karna.
- API responses mein sensitive fields (passwords, internal IDs, tokens) ko accidentally include kar dena.
- Development mein har request pe naya database-connection banana, jo production mein severe performance-bottleneck ban sakta hai high traffic ke saath.

## 11. Edge Cases

ORMs (previous chapter) generally automatically parameterized queries use karte hain internally — lekin agar tum "raw query" methods use karte ho ORM ke andar bhi (jaise complex custom queries ke liye), tumhe manually inject-safety ensure karni padti hai.

## 12. Real-World Usage

SQL Injection OWASP Top 10 (most critical web application security risks) mein historically top-listed rahi hai — almost har production database-driven application ko isse defend karna padta hai. Connection pooling har production backend application mein standard practice hai.

## 13. Comparison With Related Concepts

| Practice | Protects Against |
|----------|----------------------|
| Parameterized queries | SQL Injection |
| Connection pooling | Performance degradation under load |
| Field exclusion (sensitive data) | Accidental data exposure |
| Principle of least privilege | Damage limitation if credentials are compromised |

## 14. Practice Questions

1. SQL Injection kaise possible hoti hai (root cause)?
2. Connection pooling ka main performance-benefit kya hai?
3. API response mein sensitive fields accidentally expose hone se kaise bacha ja sakta hai?

## 15. Challenge

Ek vulnerable-looking function likho (string-concatenation wali query), phir usse parameterized-query version mein convert karo, aur comments mein explain karo exactly kya risk fix hua.

## 16. Interview Questions

**Q1: SQL Injection attack kaise kaam karta hai, aur ise kaise prevent karte hain?**
A: SQL Injection tab hoti hai jab user-provided input **directly** SQL query-string mein concatenate kiya jaata hai bina proper sanitization ke — attacker specially-crafted input (jaise `' OR '1'='1`) de sakta hai jo query ki **logic/structure ko badal deta hai**, unintended data expose kar sakta hai ya destructive operations (jaise table delete) trigger kar sakta hai. Prevention hai **parameterized queries** (ya prepared statements) use karna, jaha user-input ko query-structure se **completely separate** treat kiya jaata hai — database driver isse hamesha literal value ki tarah interpret karta hai, kabhi executable SQL code ki tarah nahi. ORMs generally ye protection automatically provide karte hain.

**Q2: Connection pooling kya hai aur ye production applications mein kyun zaroori hai?**
A: Database connection banana ek **expensive operation** hai (network handshake, authentication, resource allocation) — agar har database-query ke liye naya connection banaya jaaye aur baad mein band kiya jaaye, ye significant overhead add karta hai, especially high-traffic applications mein jaha thousands of requests per second aa sakti hain. **Connection pooling** ek fixed number of connections ko pehle se establish karke "pool" mein maintain karta hai — requests in existing connections ko reuse karte hain (borrow karke, kaam khatam hone pe wapas pool mein return karke), jisse per-request overhead drastically kam ho jaata hai aur application significantly better perform karta hai load ke under.

## 17. Chapter Summary

SQL Injection user-input ko query-structure ke saath galat tarike se mix karne se hoti hai — parameterized queries isse prevent karte hain. Connection pooling database-connection overhead ko minimize karke performance improve karta hai high-traffic scenarios mein. Sensitive fields (passwords, tokens) ko API responses se explicitly exclude karna zaroori hai accidental exposure se bachne ke liye.

## 18. Revision Checklist

- [ ] SQL Injection ka mechanism aur prevention (parameterized queries) clear hai.
- [ ] Connection pooling ka performance-benefit yaad hai.
- [ ] Sensitive-data-exclusion practice samajh gaya.

---

**Module 20 Complete!** Next Module: [21-Testing-and-Debugging/01-Testing-Fundamentals.md](../21-Testing-and-Debugging/01-Testing-Fundamentals.md)
