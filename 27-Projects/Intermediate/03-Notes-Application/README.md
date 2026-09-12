# Project: Notes Application

**Difficulty:** 🟡 Intermediate
**Modules Needed:** 06-07, 11-12
**Previous:** [02-Expense-Tracker](../02-Expense-Tracker/README.md)
**Next:** [04-Movie-Search-Application](../04-Movie-Search-Application/README.md)

---

## 1. Requirements

Ek notes-app jisme notes create, edit, delete, aur search kiye ja sakein — Google Keep-jaisa basic version.

## 2. Features

- Create/edit notes (title + content)
- Delete notes
- Search/filter notes by keyword
- Auto-save (debounced) while typing
- Persist in localStorage

## 3. Architecture

```
notes-app/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<div id="app">
  <input type="text" id="searchInput" placeholder="Search notes...">
  <button id="newNoteBtn">+ New Note</button>
  <div id="notesGrid"></div>

  <div id="editorModal" style="display:none">
    <input type="text" id="noteTitle" placeholder="Title">
    <textarea id="noteContent" placeholder="Take a note..."></textarea>
    <button id="closeEditorBtn">Done</button>
  </div>
</div>
```

**Step 2 — State and debounced auto-save (Module 22, chapter 03 applied):**
```js
let notes = JSON.parse(localStorage.getItem("notes")) || [];
let activeNoteId = null;

function saveNotes() {
  localStorage.setItem("notes", JSON.stringify(notes));
}

// Debounce utility (Module 22, chapter 03)
function debounce(fn, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

**Step 3 — CRUD operations:**
```js
function createNote() {
  const newNote = { id: Date.now(), title: "", content: "", updatedAt: Date.now() };
  notes.unshift(newNote); // Add to the beginning
  activeNoteId = newNote.id;
  saveNotes();
  openEditor(newNote);
  renderNotes();
}

function updateActiveNote(field, value) {
  const note = notes.find(n => n.id === activeNoteId); // Module 06, chapter 03!
  if (note) {
    note[field] = value;
    note.updatedAt = Date.now();
    saveNotes();
    renderNotes(); // Re-render grid to show the update (e.g., in the preview)
  }
}
const debouncedUpdate = debounce(updateActiveNote, 500); // Auto-save 500ms after typing stops

function deleteNote(id) {
  notes = notes.filter(n => n.id !== id);
  saveNotes();
  renderNotes();
}

function searchNotes(query) {
  const lowerQuery = query.toLowerCase();
  return notes.filter(n => // Module 06, chapter 03!
    n.title.toLowerCase().includes(lowerQuery) ||
    n.content.toLowerCase().includes(lowerQuery)
  );
}
```

**Step 4 — Rendering and event wiring:**
```js
function renderNotes(list = notes) {
  const grid = document.getElementById("notesGrid");
  grid.innerHTML = list.map(note => `
    <div class="note-card" data-id="${note.id}">
      <h3>${note.title || "Untitled"}</h3>
      <p>${note.content.slice(0, 100)}</p>
      <button class="delete-btn" data-id="${note.id}">Delete</button>
    </div>
  `).join("");
}

function openEditor(note) {
  activeNoteId = note.id;
  document.getElementById("noteTitle").value = note.title;
  document.getElementById("noteContent").value = note.content;
  document.getElementById("editorModal").style.display = "block";
}

document.getElementById("newNoteBtn").addEventListener("click", createNote);

document.getElementById("noteTitle").addEventListener("input", (e) => debouncedUpdate("title", e.target.value));
document.getElementById("noteContent").addEventListener("input", (e) => debouncedUpdate("content", e.target.value));

document.getElementById("closeEditorBtn").addEventListener("click", () => {
  document.getElementById("editorModal").style.display = "none";
});

document.getElementById("notesGrid").addEventListener("click", (event) => { // Event delegation!
  const id = Number(event.target.closest("[data-id]")?.dataset.id);
  if (event.target.classList.contains("delete-btn")) {
    deleteNote(id);
  } else if (event.target.closest(".note-card")) {
    const note = notes.find(n => n.id === id);
    if (note) openEditor(note);
  }
});

document.getElementById("searchInput").addEventListener("input", (event) => {
  renderNotes(searchNotes(event.target.value));
});

renderNotes();
```

## 5. Explanation

- `debounce()` (Module 22, chapter 03) prevents saving on **every single keystroke** — saves 500ms after the user stops typing, reducing unnecessary localStorage writes.
- `event.target.closest("[data-id]")` (a DOM traversal method) finds the nearest ancestor with a `data-id`, useful when clicks might land on child elements (like text inside a card) rather than the card itself.
- Search uses `.includes()` (Module 06, chapter 03) for simple substring matching across both title and content.

## 6. Debugging

- Agar auto-save kaam na kare: verify `debouncedUpdate` correctly `updateActiveNote` ko wrap kar raha hai, aur `activeNoteId` sahi se set hai.
- Agar search real-time update na ho: check `input` event listener (not `change`, jo focus-loss tak wait karta) attached hai.

## 7. Testing

Create multiple notes, edit one and wait to see debounced-save happen, search for a keyword present in only some notes, delete a note aur verify it's removed from both UI and localStorage.

## 8. Improvements

- Rich-text formatting (bold/italic) using `contenteditable` or a library.
- Note-color/label categorization.
- Pin important notes to top.

## 9. Advanced Version

Multi-device sync — backend (Module 19-20) integration jisse notes cloud mein store hon, aur real-time updates (Module 27's WebSocket-project concepts) across open tabs/devices.

---

**Next:** [04-Movie-Search-Application](../04-Movie-Search-Application/README.md)
