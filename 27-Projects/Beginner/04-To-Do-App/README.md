# Project: To-Do App

**Difficulty:** 🟢 Beginner
**Modules Needed:** 01-07, 11-12 (adds DOM, Events, localStorage)
**Previous:** [03-Quiz-App](../03-Quiz-App/README.md)
**Next:** [05-Digital-Clock](../05-Digital-Clock/README.md)

---

## 1. Requirements

Ek to-do list application jisme tasks add, complete (toggle), aur delete kiye ja sakein, aur data **persist** ho page-reload ke baad bhi (localStorage).

## 2. Features

- Add new task (form submit)
- Mark task complete/incomplete (toggle)
- Delete task
- Persist tasks in localStorage
- Show task count (remaining)

## 3. Architecture

```
todo-app/
├── index.html
├── style.css
└── script.js
```

**Data model:** `[{ id, text, completed }]` array — the "single source of truth" for the UI.

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<div id="app">
  <form id="todoForm">
    <input type="text" id="todoInput" placeholder="Add a task..." required>
    <button type="submit">Add</button>
  </form>
  <ul id="todoList"></ul>
  <p id="taskCount"></p>
</div>
```

**Step 2 — State management and persistence (Module 11, chapter 05):**
```js
let todos = JSON.parse(localStorage.getItem("todos")) || []; // Load on startup

function saveTodos() {
  localStorage.setItem("todos", JSON.stringify(todos)); // Module 16, chapter 01's JSON concept!
}
```

**Step 3 — Core CRUD logic (Module 16, chapter 03's CRUD concepts applied to client-side state):**
```js
function addTodo(text) {
  const newTodo = {
    id: Date.now(), // Simple unique ID using timestamp
    text,
    completed: false
  };
  todos.push(newTodo); // Module 06, chapter 02!
  saveTodos();
  renderTodos();
}

function toggleTodo(id) {
  const todo = todos.find(t => t.id === id); // Module 06, chapter 03!
  if (todo) {
    todo.completed = !todo.completed;
    saveTodos();
    renderTodos();
  }
}

function deleteTodo(id) {
  todos = todos.filter(t => t.id !== id); // Module 06, chapter 03 — non-mutating removal!
  saveTodos();
  renderTodos();
}
```

**Step 4 — Rendering with Event Delegation (Module 12, chapter 02 — critical for dynamic elements!):**
```js
function renderTodos() {
  const list = document.getElementById("todoList");
  list.innerHTML = ""; // Clear and re-render (simple approach for a beginner project)

  todos.forEach(todo => {
    const li = document.createElement("li");
    li.className = todo.completed ? "completed" : "";
    li.innerHTML = `
      <span class="todo-text">${todo.text}</span>
      <button class="toggle-btn" data-id="${todo.id}">✓</button>
      <button class="delete-btn" data-id="${todo.id}">✗</button>
    `;
    // ⚠️ Note: using innerHTML with todo.text is fine here since WE control the data,
    // but if 'text' came from untrusted external input, we'd need textContent (Module 23, ch 1)!
    list.appendChild(li);
  });

  const remaining = todos.filter(t => !t.completed).length;
  document.getElementById("taskCount").textContent = `${remaining} task(s) remaining`;
}

// Event delegation — ONE listener handles clicks on ALL current AND future buttons!
document.getElementById("todoList").addEventListener("click", (event) => {
  const id = Number(event.target.dataset.id);
  if (event.target.classList.contains("toggle-btn")) {
    toggleTodo(id);
  } else if (event.target.classList.contains("delete-btn")) {
    deleteTodo(id);
  }
});

document.getElementById("todoForm").addEventListener("submit", (event) => {
  event.preventDefault(); // Module 11, chapter 04!
  const input = document.getElementById("todoInput");
  if (input.value.trim()) {
    addTodo(input.value.trim());
    input.value = "";
  }
});

renderTodos(); // Initial render
```

## 5. Explanation

- **Event delegation** (Module 12, chapter 02) is critical here — todo-items dynamically create/destroy hote hain, ek single listener parent (`todoList`) pe automatically sabko (including future items) handle karta hai.
- `data-id` attributes buttons ko unke corresponding todo-item se link karte hain, `dataset.id` (Module 11) se accessible.
- `saveTodos()` + `JSON.stringify`/`parse` (Module 11, chapter 05) state ko localStorage mein persist karta hai — reload ke baad bhi data survive karta hai.

## 6. Debugging

- Agar tasks reload ke baad gayab ho jaayein: check karo `saveTodos()` har mutation (add/toggle/delete) ke baad call ho raha hai.
- Agar toggle/delete kaam na kare: verify karo `data-id` attribute HTML mein sahi se render ho raha hai, aur `Number()` conversion (Module 09) ho rahi hai comparison ke liye (IDs numbers hain, `dataset` strings deta hai).

## 7. Testing

Add multiple tasks, toggle kuch, delete kuch, phir page reload karo — verify karo state exactly wahi restore hoti hai jo reload se pehle thi.

## 8. Improvements

- "Edit task" functionality add karo.
- Filter buttons add karo ("All"/"Active"/"Completed" — Module 06's filter method).
- Drag-and-drop reordering (advanced DOM APIs).

## 9. Advanced Version

To-do app ko backend-connected banao (Module 16's Fetch API + Module 19's Express backend) taaki tasks server pe store hon, sirf localStorage mein nahi — multiple devices se access possible ho.

---

**Next:** [05-Digital-Clock](../05-Digital-Clock/README.md)
