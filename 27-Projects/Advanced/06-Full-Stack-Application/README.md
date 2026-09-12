# Project: Full-Stack JavaScript Application

**Difficulty:** 🔴 Advanced
**Modules Needed:** All (this is the capstone project)
**Previous:** [05-Ecommerce-Backend](../05-Ecommerce-Backend/README.md)
**Next:** [07-WebSocket-Application](../07-WebSocket-Application/README.md)

---

## 1. Requirements

Ek complete full-stack application jo poore course ke concepts ko integrate kare — a "Personal Task Manager with Team Sharing" — frontend + backend + database, end-to-end.

## 2. Features

- User authentication (register/login) — reusing [Authentication-System](../01-Authentication-System/README.md)
- Task CRUD, backed by a real API — reusing [REST-API](../02-REST-API/README.md) patterns
- Frontend built with vanilla JavaScript (DOM manipulation, Fetch API)
- Real task-sharing between users (basic authorization-checks)

## 3. Architecture

```
fullstack-task-manager/
├── backend/
│   ├── server.js
│   ├── routes/
│   │   ├── auth.js
│   │   └── tasks.js
│   ├── middleware/
│   │   └── authenticate.js
│   └── package.json
└── frontend/
    ├── index.html
    ├── style.css
    └── app.js
```

**Data flow:** Browser (frontend/app.js) → `fetch()` calls (Module 16) → Express API (backend) → Database (Module 20) → JSON response → Frontend updates DOM (Module 11).

## 4. Step-by-Step Build Process

**Step 1 — Backend setup:** Combine [Authentication-System](../01-Authentication-System/README.md)'s `auth.js`/`authenticate.js` with [REST-API](../02-REST-API/README.md)'s CRUD-pattern, but add **user-ownership** to tasks:
```js
// backend/routes/tasks.js
router.get("/", authenticate, async (req, res, next) => {
  try {
    const tasks = await db.query("SELECT * FROM tasks WHERE user_id = $1", [req.user.userId]); // Module 23, ch 03's authorization!
    res.json(tasks);
  } catch (error) {
    next(error);
  }
});

router.post("/", authenticate, async (req, res, next) => {
  try {
    const { title } = req.body;
    if (!title) return res.status(400).json({ error: "Title is required" }); // Module 19's validation!
    const task = await db.query(
      "INSERT INTO tasks (title, user_id, completed) VALUES ($1, $2, false) RETURNING *",
      [title, req.user.userId]
    );
    res.status(201).json(task);
  } catch (error) {
    next(error);
  }
});
```

**Step 2 — Frontend: a token-aware API client (Module 16 + Module 19, chapter 03's Bearer-token pattern):**
```js
// frontend/app.js
const API_BASE = "http://localhost:3000/api";
let authToken = localStorage.getItem("authToken"); // Module 11, chapter 05!

async function apiRequest(endpoint, options = {}) {
  const response = await fetch(`${API_BASE}${endpoint}`, {
    ...options,
    headers: {
      "Content-Type": "application/json",
      ...(authToken && { Authorization: `Bearer ${authToken}` }), // Conditional spread!
      ...options.headers
    }
  });

  if (response.status === 401) {
    logout(); // Token invalid/expired — force re-login
    throw new Error("Session expired, please log in again");
  }
  if (!response.ok) { // Module 16, chapter 02's critical fetch-gotcha!
    const errorData = await response.json();
    throw new Error(errorData.error || "Request failed");
  }
  return response.status === 204 ? null : response.json();
}
```

**Step 3 — Auth flow (frontend):**
```js
async function login(email, password) {
  const data = await apiRequest("/auth/login", {
    method: "POST",
    body: JSON.stringify({ email, password })
  });
  authToken = data.token;
  localStorage.setItem("authToken", authToken);
  showTaskDashboard();
}

function logout() {
  authToken = null;
  localStorage.removeItem("authToken");
  showLoginForm();
}
```

**Step 4 — Task management (frontend, combining Module 27's To-Do-App UI-patterns with real API calls):**
```js
async function loadTasks() {
  try {
    const tasks = await apiRequest("/tasks");
    renderTasks(tasks);
  } catch (error) {
    console.error("Failed to load tasks:", error.message);
  }
}

async function addTask(title) {
  await apiRequest("/tasks", { method: "POST", body: JSON.stringify({ title }) });
  loadTasks(); // Re-fetch to stay in sync with the server (the source of truth)
}

async function toggleTask(id, completed) {
  await apiRequest(`/tasks/${id}`, { method: "PATCH", body: JSON.stringify({ completed: !completed }) });
  loadTasks();
}

function renderTasks(tasks) {
  document.getElementById("taskList").innerHTML = tasks.map(task => `
    <li class="${task.completed ? "completed" : ""}">
      <span>${task.title}</span>
      <button data-id="${task.id}" data-completed="${task.completed}" class="toggle-btn">✓</button>
    </li>
  `).join("");
}

document.getElementById("taskList").addEventListener("click", (event) => { // Module 12, chapter 02!
  if (event.target.classList.contains("toggle-btn")) {
    const { id, completed } = event.target.dataset;
    toggleTask(Number(id), completed === "true");
  }
});
```

## 5. Explanation

- The frontend **never talks to the database directly** — everything goes through the authenticated API, which is the correct, secure architecture (Module 23's principles).
- `apiRequest()` centralizes token-attachment and error-handling — a DRY pattern (Module 26, chapter 01) that every API-call reuses.
- After every mutation (add/toggle), the frontend **re-fetches** from the server rather than trying to locally predict the new state — simpler and less error-prone for a learning-project (production apps often do optimistic-updates instead, a more advanced technique).

## 6. Debugging

- Agar frontend "Session expired" baar-baar dikhaye: verify token `localStorage` mein correctly save/retrieve ho raha hai, aur backend/frontend `JWT_SECRET`/expiry consistent hain.
- Agar CORS-errors aayein (frontend/backend different ports pe): add CORS-middleware (Module 16, chapter 04's CORS-concepts) to the Express backend — `app.use(cors())` with the `cors` npm package for local development.

## 7. Testing

Full end-to-end flow: register → login → add tasks → toggle completion → logout → verify login-again shows the same persisted tasks (proving the database, not just local-state, is the source of truth).

## 8. Improvements

- Add real task-sharing (invite another user to view/edit specific tasks) — a genuine multi-user authorization challenge (Module 23, chapter 03).
- Add optimistic UI updates (update UI immediately, roll back if the API call fails) for a snappier feel.

## 9. Advanced Version

Deploy the application (backend to a cloud-host, frontend to a static-host), using real environment-variables (Module 18, chapter 03) for production configuration, and add basic monitoring/logging (Module 19, chapter 04).

---

**Next:** [07-WebSocket-Application](../07-WebSocket-Application/README.md)
