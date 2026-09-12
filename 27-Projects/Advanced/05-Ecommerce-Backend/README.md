# Project: E-commerce Backend

**Difficulty:** 🔴 Advanced
**Modules Needed:** 19-20, 23
**Previous:** [04-Notification-System](../04-Notification-System/README.md)
**Next:** [06-Full-Stack-Application](../06-Full-Stack-Application/README.md)

---

## 1. Requirements

Ek complete e-commerce backend jo products, cart, aur orders manage kare — proper database-transactions (Module 20, chapter 03) ke saath, taaki inventory kabhi inconsistent state mein na jaaye.

## 2. Features

- Product catalog (CRUD)
- Cart management (per-user)
- Order-placement with **transactional** inventory-deduction
- Authentication-protected routes (reusing Module 27's Auth-System project)

## 3. Architecture

```
ecommerce-backend/
├── server.js
├── package.json
├── .env
├── routes/
│   ├── products.js
│   ├── cart.js
│   └── orders.js
├── middleware/
│   └── authenticate.js       (from Authentication-System project)
└── models/                     (conceptual — using an ORM, Module 20 ch 03, in a real build)
    ├── Product.js
    ├── Cart.js
    └── Order.js
```

## 4. Step-by-Step Build Process

**Step 1 — Product routes (Module 16 chapter 03's REST + Module 20 chapter 02's CRUD):**
```js
// routes/products.js
const express = require("express");
const router = express.Router();

// Assume 'db' is a database connection (Module 20) — pseudocode-style for portability across DB choices

router.get("/", async (req, res, next) => {
  try {
    const products = await db.query("SELECT id, name, price, stock FROM products");
    res.json(products);
  } catch (error) {
    next(error); // Module 19, chapter 04!
  }
});

router.get("/:id", async (req, res, next) => {
  try {
    const product = await db.query("SELECT * FROM products WHERE id = $1", [req.params.id]); // Module 20, ch 04's parameterized query!
    if (!product) return res.status(404).json({ error: "Product not found" });
    res.json(product);
  } catch (error) {
    next(error);
  }
});

module.exports = router;
```

**Step 2 — The critical part: transactional order-placement (Module 20, chapter 03's Atomicity, directly applied):**
```js
// routes/orders.js
const express = require("express");
const authenticate = require("../middleware/authenticate"); // From the Auth-System project
const router = express.Router();

router.post("/", authenticate, async (req, res, next) => {
  const { items } = req.body; // [{ productId, quantity }]
  const userId = req.user.userId; // Set by the authenticate middleware — Module 19, ch 03!

  const transaction = await db.beginTransaction(); // Module 20, chapter 03!

  try {
    let totalAmount = 0;

    for (const item of items) {
      const product = await db.query(
        "SELECT * FROM products WHERE id = $1 FOR UPDATE", // Lock the row during the transaction
        [item.productId],
        { transaction }
      );

      if (!product || product.stock < item.quantity) {
        throw new Error(`Insufficient stock for product ${item.productId}`);
      }

      await db.query(
        "UPDATE products SET stock = stock - $1 WHERE id = $2",
        [item.quantity, item.productId],
        { transaction }
      );

      totalAmount += product.price * item.quantity;
    }

    const order = await db.query(
      "INSERT INTO orders (user_id, total_amount, status) VALUES ($1, $2, 'confirmed') RETURNING *",
      [userId, totalAmount],
      { transaction }
    );

    await transaction.commit(); // ALL succeeded — make it permanent (Module 20, ch 03!)
    res.status(201).json(order);
  } catch (error) {
    await transaction.rollback(); // ANY step failed — undo EVERYTHING (Module 20, ch 03!)
    next(error);
  }
});

module.exports = router;
```
**Why this matters (revisiting Module 20, chapter 03):** Without a transaction, if inventory-deduction succeeds for item 1 but fails for item 2 (out of stock), you'd end up with **inconsistent state** — item 1's stock permanently reduced even though the whole order failed. The transaction ensures it's all-or-nothing.

**Step 3 — Cart routes (simpler CRUD, Module 20 chapter 02 patterns):**
```js
// routes/cart.js
router.post("/add", authenticate, async (req, res, next) => {
  try {
    const { productId, quantity } = req.body;
    await db.query(
      "INSERT INTO cart_items (user_id, product_id, quantity) VALUES ($1, $2, $3)",
      [req.user.userId, productId, quantity]
    );
    res.status(201).json({ message: "Added to cart" });
  } catch (error) {
    next(error);
  }
});
```

**Step 4 — `server.js`:**
```js
require("dotenv").config();
const express = require("express");
const productRoutes = require("./routes/products");
const cartRoutes = require("./routes/cart");
const orderRoutes = require("./routes/orders");

const app = express();
app.use(express.json());
app.use("/api/products", productRoutes);
app.use("/api/cart", cartRoutes);
app.use("/api/orders", orderRoutes);

app.use((error, req, res, next) => { // Module 19, chapter 04!
  console.error(error.stack);
  res.status(500).json({ error: error.message.includes("Insufficient stock") ? error.message : "Internal Server Error" });
});

app.listen(3000, () => console.log("E-commerce backend running"));
```

## 5. Explanation

- Order-placement is the architectural centerpiece — it demonstrates **why** transactions (Module 20, chapter 03) matter in a real business-scenario, not just as an abstract concept.
- `FOR UPDATE` (a SQL row-locking clause) prevents a race-condition where two simultaneous orders could both "see" enough stock and both proceed, over-selling the product — an advanced but important real-world concern.
- Authorization (Module 23, chapter 03) is implicit here via `req.user.userId` — orders are always created for the authenticated user, never an arbitrary user-ID from the request-body.

## 6. Debugging

- Agar transaction "hang" ho jaaye: verify **every** code-path (success AND error) either calls `commit()` or `rollback()` — a missing rollback in an error-path can leave a transaction open indefinitely.
- Agar stock inconsistent dikhe under concurrent load: verify row-locking (`FOR UPDATE` or equivalent) is actually being used.

## 7. Testing

Simulate placing an order with sufficient stock (should succeed, stock decreases), then an order exceeding available stock (should fail entirely — verify NO partial stock-deduction happened for other items in that same order).

## 8. Improvements

- Add order-status transitions (pending → confirmed → shipped → delivered).
- Add a proper payment-integration simulation (or a real one, like Stripe's test-mode).

## 9. Advanced Version

Add an admin-dashboard API (separate authorization-level, Module 23 chapter 03) for managing inventory, viewing all orders, and generating sales-reports (reusing Module 27's Dashboard-project aggregation-patterns).

---

**Next:** [06-Full-Stack-Application](../06-Full-Stack-Application/README.md)
