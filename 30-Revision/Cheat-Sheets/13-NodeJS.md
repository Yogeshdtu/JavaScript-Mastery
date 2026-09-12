# Cheat Sheet: Node.js

**Full detail:** [18-NodeJS/](../../18-NodeJS/), [19-Backend-JavaScript/](../../19-Backend-JavaScript/)

---

## Core Modules
```js
const fs = require("fs/promises");    // File system (async)
const path = require("path");           // Cross-platform paths
const http = require("http");            // Raw HTTP server
```

## package.json Essentials
```json
{
  "dependencies": {},     // Production packages
  "devDependencies": {},   // Dev/test-only packages
  "scripts": { "start": "node index.js" }
}
```
- `package-lock.json` — commit it! Ensures consistent installs.
- `node_modules/` — never commit (`.gitignore`).

## Environment Variables
```js
require("dotenv").config();
process.env.API_KEY // Always a STRING, even for numbers/booleans
```

## Express Essentials
```js
const app = express();
app.use(express.json());               // Body-parsing middleware
app.get("/path", (req, res) => {});     // Route
app.use((req, res, next) => { next(); }); // Middleware — MUST call next()!
app.use((err, req, res, next) => {});    // Error handler (4 params, LAST)
```

## Auth Pattern
```js
const hashed = await bcrypt.hash(password, 10);   // Hash before storing
const match = await bcrypt.compare(password, hashed);
const token = jwt.sign({ userId }, SECRET, { expiresIn: "1h" });
jwt.verify(token, SECRET);
```

## Security Checklist
- ✅ Parameterized queries (never string-concat SQL)
- ✅ `httpOnly` cookies for sensitive tokens
- ✅ Check ownership, not just authentication
- ✅ `npm audit` regularly

---

**Cheat Sheets Complete!** Next: [30-Revision/Quick-Revision/01-Foundations-to-Functions.md](../Quick-Revision/01-Foundations-to-Functions.md)
