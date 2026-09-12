# Cheat Sheet: Fetch API and Modules

**Full detail:** [16-Fetch-and-APIs/](../../16-Fetch-and-APIs/), [17-Modules/](../../17-Modules/)

---

## Fetch API
```js
const response = await fetch(url, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(data)
});

if (!response.ok) throw new Error(`HTTP ${response.status}`); // ⚠️ CRITICAL — fetch doesn't reject on 4xx/5xx!
const data = await response.json();
```

## HTTP Status Codes
| Range | Meaning |
|-------|---------|
| 2xx | Success (200 OK, 201 Created, 204 No Content) |
| 4xx | Client error (400, 401, 403, 404, 429) |
| 5xx | Server error (500, 503) |

## REST + CRUD Mapping
| CRUD | HTTP Method |
|------|-------------|
| Create | POST |
| Read | GET |
| Update | PUT (full) / PATCH (partial) |
| Delete | DELETE |

## ES Modules (✅ Modern)
```js
export const x = 1;
export default function() {}
import { x } from "./file.js";
import myDefault from "./file.js";
import * as All from "./file.js";
```

## CommonJS (⚠️ Legacy, Node.js)
```js
module.exports = { x };
const { x } = require("./file.js");
```

## Key ESM vs CommonJS Differences
| | CommonJS | ES Modules |
|---|---|---|
| Loading | Synchronous | Async-capable |
| Browser support | No (needs bundler) | Yes, native |
| Analysis | Dynamic | Static |

---

**Next:** [13-NodeJS.md](./13-NodeJS.md)
