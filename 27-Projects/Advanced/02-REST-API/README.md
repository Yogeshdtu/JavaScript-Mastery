# Project: REST API

**Difficulty:** 🔴 Advanced
**Modules Needed:** 18-20
**Previous:** [01-Authentication-System](../01-Authentication-System/README.md)
**Next:** [03-Real-Time-Chat](../03-Real-Time-Chat/README.md)

---

## 1. Requirements

Ek complete, well-structured REST API for a "Task Manager" resource — full CRUD, proper status-codes, validation, aur error-handling.

## 2. Features

- Full CRUD endpoints for tasks
- Input validation middleware
- Pagination support
- Proper REST conventions (Module 16, chapter 03)
- Centralized error-handling (Module 19, chapter 04)

## 3. Architecture

```
task-api/
├── server.js
├── package.json
├── routes/
│   └── tasks.js
├── middleware/
│   ├── validateTask.js
│   └── errorHandler.js
└── data/
    └── taskStore.js
```

## 4. Step-by-Step Build Process

**Step 1 — `data/taskStore.js` (simulating a database layer — swap for real DB, Module 20):**
```js
let tasks = [
  { id: 1, title: "Learn Express", completed: false, createdAt: new Date().toISOString() }
];
let nextId = 2;

module.exports = {
  getAll: () => tasks,
  getById: (id) => tasks.find(t => t.id === id),
  create: (data) => {
    const task = { id: nextId++, completed: false, createdAt: new Date().toISOString(), ...data };
    tasks.push(task);
    return task;
  },
  update: (id, data) => {
    const task = tasks.find(t => t.id === id);
    if (!task) return null;
    Object.assign(task, data); // Module 07, chapter 03!
    return task;
  },
  remove: (id) => {
    const index = tasks.findIndex(t => t.id === id);
    if (index === -1) return false;
    tasks.splice(index, 1); // Module 06, chapter 02!
    return true;
  }
};
```

**Step 2 — `middleware/validateTask.js` (Module 19, chapter 02):**
```js
function validateTask(req, res, next) {
  const { title } = req.body;
  if (!title || typeof title !== "string" || title.trim().length === 0) {
    return res.status(400).json({ error: "Task 'title' is required and must be a non-empty string" });
  }
  next();
}
module.exports = validateTask;
```

**Step 3 — `routes/tasks.js` (full CRUD, Module 16 chapter 03 + Module 19 chapter 02 combined):**
```js
const express = require("express");
const taskStore = require("../data/taskStore");
const validateTask = require("../middleware/validateTask");

const router = express.Router();

// READ (all) — with pagination (Module 16, chapter 04)
router.get("/", (req, res) => {
  const page = Number(req.query.page) || 1;
  const limit = Number(req.query.limit) || 10;
  const allTasks = taskStore.getAll();

  const startIndex = (page - 1) * limit;
  const paginatedTasks = allTasks.slice(startIndex, startIndex + limit); // Module 06, chapter 03!

  res.json({
    data: paginatedTasks,
    currentPage: page,
    totalPages: Math.ceil(allTasks.length / limit),
    totalItems: allTasks.length
  });
});

// READ (one)
router.get("/:id", (req, res) => {
  const task = taskStore.getById(Number(req.params.id));
  if (!task) return res.status(404).json({ error: "Task not found" });
  res.json(task);
});

// CREATE
router.post("/", validateTask, (req, res) => {
  const newTask = taskStore.create({ title: req.body.title });
  res.status(201).json(newTask); // 201 Created — Module 16, chapter 01!
});

// UPDATE (partial)
router.patch("/:id", (req, res) => {
  const updated = taskStore.update(Number(req.params.id), req.body);
  if (!updated) return res.status(404).json({ error: "Task not found" });
  res.json(updated);
});

// DELETE
router.delete("/:id", (req, res) => {
  const deleted = taskStore.remove(Number(req.params.id));
  if (!deleted) return res.status(404).json({ error: "Task not found" });
  res.status(204).send(); // 204 No Content — Module 16, chapter 01!
});

module.exports = router;
```

**Step 4 — `middleware/errorHandler.js` (Module 19, chapter 04):**
```js
function errorHandler(error, req, res, next) {
  console.error(error.stack); // Log details server-side
  res.status(500).json({ error: "Internal Server Error" }); // Generic message client-side — Module 23, ch 04!
}
module.exports = errorHandler;
```

**Step 5 — `server.js`:**
```js
const express = require("express");
const taskRoutes = require("./routes/tasks");
const errorHandler = require("./middleware/errorHandler");

const app = express();
app.use(express.json());
app.use("/api/tasks", taskRoutes);

app.use((req, res) => res.status(404).json({ error: "Route not found" })); // Catch-all — Module 19, ch 01!
app.use(errorHandler); // Must be LAST — Module 19, chapter 02!

app.listen(3000, () => console.log("API running on port 3000"));
```

## 5. Explanation

- Data-access is abstracted into `taskStore.js` — routes don't know/care whether data comes from an array or a real database, making a future migration to Module 20's SQL/MongoDB straightforward.
- Correct status-codes throughout (200, 201, 204, 404, 400, 500) follow HTTP-semantics from Module 16, chapter 01.
- Pagination (Module 16, chapter 04) prevents returning unbounded result-sets.

## 6. Debugging

- Agar 404-catch-all har request pe trigger ho (even valid ones): verify route-registration-order — specific routes must come before the catch-all (Module 19, chapter 01's routing-order gotcha).
- Agar validation-errors na aayein: verify `validateTask` middleware sahi route pe, sahi order mein attached hai.

## 7. Testing

Use Postman/curl/Thunder-Client to test every endpoint — including edge-cases like requesting a non-existent ID, submitting an empty title, and paginating past the last page.

## 8. Improvements

- Add filtering (`?completed=true`) and sorting (`?sort=createdAt`) query-parameters.
- Migrate `taskStore.js` to use a real database with an ORM (Module 20, chapter 03).

## 9. Advanced Version

Add authentication (integrate Module 27's Authentication-System project) so tasks are user-specific, with proper authorization-checks (Module 23, chapter 03's "check ownership, not just login" principle).

---

**Next:** [03-Real-Time-Chat](../03-Real-Time-Chat/README.md)
