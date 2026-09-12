# Project: WebSocket Application — Live Collaborative Board

**Difficulty:** 🔴 Advanced
**Modules Needed:** 11, 18 (extends Module 27's Real-Time-Chat project)
**Previous:** [06-Full-Stack-Application](../06-Full-Stack-Application/README.md)

---

## 1. Requirements

Ek real-time "collaborative sticky-note board" jaha multiple users ek saath notes add/move/edit kar sakein, aur sabko turant har change dikhe — Trello/Miro jaisa basic version.

## 2. Features

- Real-time note creation (visible to all connected clients instantly)
- Real-time note-position updates (drag-and-drop, synced)
- Connected-users count/list
- State-sync for newly-joining clients (they see the current board, not just future changes)

## 3. Architecture

```
collab-board/
├── server.js
├── package.json
└── public/
    ├── index.html
    ├── style.css
    └── client.js
```

This project extends [Real-Time-Chat](../03-Real-Time-Chat/README.md)'s WebSocket-foundation with **shared, synchronized state** (not just message-broadcasting).

## 4. Step-by-Step Build Process

**Step 1 — Server-side shared state (Module 18, chapter 04's EventEmitter-adjacent patterns, Module 02's reference-types):**
```js
const express = require("express");
const http = require("http");
const WebSocket = require("ws");
const path = require("path");

const app = express();
const server = http.createServer(app);
const wss = new WebSocket.Server({ server });

app.use(express.static(path.join(__dirname, "public")));

// The SHARED STATE — this is the "single source of truth" all clients sync to
let boardState = {
  notes: [] // [{ id, text, x, y, color }]
};

let connectedUsers = 0;

wss.on("connection", (ws) => {
  connectedUsers++;
  console.log(`Client connected. Total: ${connectedUsers}`);

  // CRITICAL: send the CURRENT state to the newly-joined client immediately
  ws.send(JSON.stringify({ type: "init", boardState, connectedUsers }));

  broadcastUserCount();

  ws.on("message", (rawMessage) => {
    const data = JSON.parse(rawMessage);
    handleClientMessage(data, ws);
  });

  ws.on("close", () => {
    connectedUsers--;
    broadcastUserCount();
  });

  ws.on("error", (error) => console.error("WebSocket error:", error)); // Module 18, ch 04!
});

function handleClientMessage(data, senderWs) {
  switch (data.type) {
    case "addNote": {
      const newNote = { id: Date.now(), text: data.text, x: data.x, y: data.y, color: data.color };
      boardState.notes.push(newNote); // Module 06, chapter 02!
      broadcast({ type: "noteAdded", note: newNote }, senderWs);
      break;
    }
    case "moveNote": {
      const note = boardState.notes.find(n => n.id === data.id); // Module 06, chapter 03!
      if (note) {
        note.x = data.x;
        note.y = data.y;
        broadcast({ type: "noteMoved", id: data.id, x: data.x, y: data.y }, senderWs);
      }
      break;
    }
    case "deleteNote": {
      boardState.notes = boardState.notes.filter(n => n.id !== data.id); // Module 06, chapter 03!
      broadcast({ type: "noteDeleted", id: data.id }, senderWs);
      break;
    }
  }
}

function broadcast(data, excludeWs = null) {
  const message = JSON.stringify(data);
  wss.clients.forEach(client => {
    if (client !== excludeWs && client.readyState === WebSocket.OPEN) {
      client.send(message);
    }
  });
}

function broadcastUserCount() {
  broadcast({ type: "userCount", count: connectedUsers });
}

server.listen(3000, () => console.log("Collab board running on port 3000"));
```

**Step 2 — Client-side (Module 11's DOM manipulation + Module 11 chapter 06's WebSocket API):**
```js
// public/client.js
const socket = new WebSocket(`ws://${window.location.host}`);
const board = document.getElementById("board");

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);

  switch (data.type) {
    case "init":
      data.boardState.notes.forEach(renderNote); // Sync existing state on join!
      updateUserCount(data.connectedUsers);
      break;
    case "noteAdded":
      renderNote(data.note);
      break;
    case "noteMoved":
      moveNoteElement(data.id, data.x, data.y);
      break;
    case "noteDeleted":
      document.getElementById(`note-${data.id}`)?.remove();
      break;
    case "userCount":
      updateUserCount(data.count);
      break;
  }
};

function renderNote(note) {
  const noteEl = document.createElement("div");
  noteEl.id = `note-${note.id}`;
  noteEl.className = "sticky-note";
  noteEl.style.left = `${note.x}px`;
  noteEl.style.top = `${note.y}px`;
  noteEl.style.background = note.color;
  noteEl.textContent = note.text;
  makeDraggable(noteEl, note.id);
  board.appendChild(noteEl);
}

function makeDraggable(element, noteId) {
  element.addEventListener("mousedown", (e) => {
    const offsetX = e.clientX - element.offsetLeft;
    const offsetY = e.clientY - element.offsetTop;

    function onMouseMove(moveEvent) {
      const x = moveEvent.clientX - offsetX;
      const y = moveEvent.clientY - offsetY;
      element.style.left = `${x}px`;
      element.style.top = `${y}px`;
      socket.send(JSON.stringify({ type: "moveNote", id: noteId, x, y })); // Broadcast the drag in real-time!
    }

    function onMouseUp() {
      document.removeEventListener("mousemove", onMouseMove); // Module 12, chapter 01's cleanup!
      document.removeEventListener("mouseup", onMouseUp);
    }

    document.addEventListener("mousemove", onMouseMove);
    document.addEventListener("mouseup", onMouseUp);
  });
}

function moveNoteElement(id, x, y) {
  const el = document.getElementById(`note-${id}`);
  if (el) {
    el.style.left = `${x}px`;
    el.style.top = `${y}px`;
  }
}

function updateUserCount(count) {
  document.getElementById("userCount").textContent = `${count} user(s) online`;
}

document.getElementById("addNoteBtn").addEventListener("click", () => {
  const text = prompt("Note text:");
  if (text) {
    socket.send(JSON.stringify({
      type: "addNote", text, x: 50, y: 50,
      color: ["#ffeb3b", "#4caf50", "#2196f3", "#ff5722"][Math.floor(Math.random() * 4)]
    }));
  }
});
```

## 5. Explanation

- The **"init" message on connection** (sending `boardState` to a newly-joined client) is the key pattern that distinguishes this from simple chat (Module 27's Real-Time-Chat) — new clients need to **catch up** to the current shared state, not just hear about future changes.
- Server holds the **authoritative state** (`boardState`) — clients never trust their own local view alone; every mutation goes through the server, which then broadcasts the confirmed change to everyone (including, implicitly, back-confirming to the sender in more robust implementations).
- Real-time drag-broadcasting (`onMouseMove` sending WebSocket-messages continuously) demonstrates a genuinely high-frequency real-time use-case — in a production app, this stream would likely be throttled (Module 22, chapter 03) to reduce network-traffic.

## 6. Debugging

- Agar naye clients ko existing notes na dikhein: verify server "init" message mein current `boardState` include kar raha hai, aur client `data.boardState.notes.forEach(renderNote)` correctly process kar raha hai.
- Agar drag laggy/jumpy lage across clients: consider throttling (Module 22, chapter 03) the `moveNote` messages instead of sending on every single `mousemove` event.

## 7. Testing

Open the board in 2+ browser-windows, add a note in one — verify it instantly appears in the other. Drag a note in one window — verify smooth, real-time position-sync in the other. Refresh one window — verify it re-syncs to the current board-state on reconnect.

## 8. Improvements

- Throttle the drag-broadcast (Module 22, chapter 03) to reduce network-load.
- Persist `boardState` to a database (Module 20) so it survives server-restarts.

## 9. Advanced Version

Add "presence" cursors (show where each connected user's mouse currently is, in real-time — a hallmark feature of tools like Figma/Google Docs) and undo/redo (Module 25, chapter 02's Command pattern).

---

**🎉 All 20 Projects Complete!** You've now built projects spanning the entire spectrum — from a simple calculator to a real-time collaborative application. Next: [28-Coding-Problems](../../../28-Coding-Problems/00-How-To-Use-This-Library.md) to sharpen your problem-solving, then [29-Interview-Preparation](../../../29-Interview-Preparation/01-Basic-Questions.md).
