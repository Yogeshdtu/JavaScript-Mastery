# Project: Real-Time Chat

**Difficulty:** 🔴 Advanced
**Modules Needed:** 11, 18 (adds WebSockets from Module 11, chapter 06)
**Previous:** [02-REST-API](../02-REST-API/README.md)
**Next:** [04-Notification-System](../04-Notification-System/README.md)

---

## 1. Requirements

Ek real-time chat-application jisme multiple users ek "room" mein messages exchange kar sakein, bina page-reload ke, WebSockets use karke.

## 2. Features

- Real-time message-broadcasting
- Username-based identification
- "User joined/left" notifications
- Basic message-history (in-memory, per session)

## 3. Architecture

```
realtime-chat/
├── server.js       (Node.js WebSocket server)
├── package.json
└── public/
    ├── index.html
    ├── style.css
    └── client.js   (browser-side WebSocket client)
```

**Why WebSockets (not HTTP polling)?** HTTP request/response (Module 16) is fundamentally **one-directional-per-request** — client asks, server answers. Chat needs the **server to push data to clients** without them asking (a new message from another user). WebSockets (Module 11, chapter 06) provide a persistent, bidirectional connection perfect for this.

## 4. Step-by-Step Build Process

**Step 1 — Setup:**
```bash
npm init -y
npm install ws express
```

**Step 2 — `server.js` (combining Module 18's `http` module concepts with the `ws` library):**
```js
const express = require("express");
const http = require("http");
const WebSocket = require("ws");
const path = require("path");

const app = express();
const server = http.createServer(app); // Module 18, chapter 05's http.createServer, reused!
const wss = new WebSocket.Server({ server });

app.use(express.static(path.join(__dirname, "public")));

const clients = new Map(); // ws-connection -> username (Module 10, chapter 04's Map!)

wss.on("connection", (ws) => { // EventEmitter pattern — Module 18, chapter 04!
  console.log("A new client connected");

  ws.on("message", (rawMessage) => {
    const data = JSON.parse(rawMessage); // Module 16, chapter 01's JSON!

    if (data.type === "join") {
      clients.set(ws, data.username);
      broadcast({ type: "system", text: `${data.username} joined the chat` }, ws);
    } else if (data.type === "message") {
      const username = clients.get(ws);
      broadcast({ type: "message", username, text: data.text, timestamp: Date.now() });
    }
  });

  ws.on("close", () => {
    const username = clients.get(ws);
    if (username) {
      broadcast({ type: "system", text: `${username} left the chat` });
      clients.delete(ws);
    }
  });

  ws.on("error", (error) => { // Module 18, chapter 04's critical 'error' event handling!
    console.error("WebSocket error:", error);
  });
});

function broadcast(data, excludeWs = null) {
  const message = JSON.stringify(data);
  wss.clients.forEach(client => { // Iterating all connected clients
    if (client !== excludeWs && client.readyState === WebSocket.OPEN) {
      client.send(message);
    }
  });
}

const PORT = 3000;
server.listen(PORT, () => console.log(`Chat server running on port ${PORT}`));
```

**Step 3 — `public/client.js` (browser-side, Module 11 chapter 06's WebSocket API):**
```js
const socket = new WebSocket(`ws://${window.location.host}`);
let username = "";

socket.onopen = () => {
  username = prompt("Enter your username:") || "Anonymous";
  socket.send(JSON.stringify({ type: "join", username }));
};

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  const messagesDiv = document.getElementById("messages");

  const messageEl = document.createElement("div");
  if (data.type === "system") {
    messageEl.className = "system-message";
    messageEl.textContent = data.text;
  } else {
    messageEl.className = "chat-message";
    messageEl.textContent = `${data.username}: ${data.text}`;
  }
  messagesDiv.appendChild(messageEl);
  messagesDiv.scrollTop = messagesDiv.scrollHeight; // Auto-scroll to latest message
};

socket.onclose = () => {
  console.log("Disconnected from chat server");
};

document.getElementById("chatForm").addEventListener("submit", (event) => {
  event.preventDefault(); // Module 11, chapter 04!
  const input = document.getElementById("messageInput");
  if (input.value.trim()) {
    socket.send(JSON.stringify({ type: "message", text: input.value }));
    input.value = "";
  }
});
```

## 5. Explanation

- `clients` (a `Map`, Module 10 chapter 04) associates each WebSocket-connection-object with its username — a good use-case since keys are objects (connections), not strings.
- `broadcast()` iterates `wss.clients` (all connected sockets) and sends to each — this is the "publish" side of a Pub/Sub pattern (Module 25, chapter 02).
- The `'error'` event handler on `ws` is critical (Module 18, chapter 04's warning) — without it, a WebSocket-error could crash the Node.js process.

## 6. Debugging

- Agar messages sirf sender ko dikhein, doosron ko nahi: verify `broadcast()` correctly `wss.clients` iterate kar raha hai, `excludeWs` logic sahi hai.
- Agar connection turant close ho jaaye: check browser-console aur server-logs dono — `ws.on("error")` handler add karo agar missing hai.

## 7. Testing

Open multiple browser-tabs/windows pointing to the same server, join with different usernames, send messages from each — verify all tabs receive all messages in real-time, and join/leave notifications appear correctly.

## 8. Improvements

- Add multiple "rooms" (channels) so users can join specific topic-based chats.
- Persist chat-history (Module 20's database) so it survives server-restarts.

## 9. Advanced Version

Add typing-indicators ("User is typing..."), read-receipts, and private (1-on-1) messaging alongside the public room.

---

**Next:** [04-Notification-System](../04-Notification-System/README.md)
