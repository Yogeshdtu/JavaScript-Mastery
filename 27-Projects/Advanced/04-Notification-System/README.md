# Project: Notification System

**Difficulty:** 🔴 Advanced
**Modules Needed:** 18-19, 25
**Previous:** [03-Real-Time-Chat](../03-Real-Time-Chat/README.md)
**Next:** [05-Ecommerce-Backend](../05-Ecommerce-Backend/README.md)

---

## 1. Requirements

Ek backend notification-system jo different events (jaise "order placed", "user registered") pe multiple notification-channels (email-simulation, SMS-simulation, in-app) ko trigger kare — Pub/Sub aur Strategy patterns use karke.

## 2. Features

- Event-driven architecture (EventEmitter-based)
- Multiple notification-channels (pluggable/swappable)
- Notification-history log
- Simple REST endpoint to trigger events

## 3. Architecture

```
notification-system/
├── server.js
├── package.json
├── events/
│   └── notificationEmitter.js
├── channels/
│   ├── emailChannel.js
│   ├── smsChannel.js
│   └── inAppChannel.js
└── routes/
    └── events.js
```

**Design rationale:** Ye project Module 25's design-patterns ko real-world architecture mein apply karta hai — **Observer/Pub-Sub** (Module 25, chapter 02) events ko decouple karta hai unke handlers se, aur **Strategy pattern** (Module 25, chapter 01/02) har channel ko interchangeable banata hai.

## 4. Step-by-Step Build Process

**Step 1 — `events/notificationEmitter.js` (Module 18, chapter 04's EventEmitter; Module 25, chapter 02's Observer):**
```js
const EventEmitter = require("events");

class NotificationEmitter extends EventEmitter {}

const notificationEmitter = new NotificationEmitter();

notificationEmitter.on("error", (err) => { // Module 18, chapter 04's critical warning!
  console.error("Notification system error:", err);
});

module.exports = notificationEmitter;
```

**Step 2 — Channel modules, each following the same "Strategy" interface (Module 25, chapters 01-02):**
```js
// channels/emailChannel.js
function sendEmail(user, message) {
  console.log(`📧 [EMAIL] To: ${user.email} — ${message}`);
  // In production: integrate a real email-service (SendGrid, etc.)
}
module.exports = sendEmail;

// channels/smsChannel.js
function sendSMS(user, message) {
  console.log(`📱 [SMS] To: ${user.phone} — ${message}`);
}
module.exports = sendSMS;

// channels/inAppChannel.js
const notificationLog = []; // In-memory "notification inbox" per this project's scope

function sendInApp(user, message) {
  notificationLog.push({ userId: user.id, message, timestamp: Date.now() });
  console.log(`🔔 [IN-APP] For user ${user.id} — ${message}`);
}
module.exports = { sendInApp, notificationLog };
```

**Step 3 — Wiring channels to events (the "subscription" side of Pub/Sub, Module 25 chapter 02):**
```js
// server.js (relevant section)
const notificationEmitter = require("./events/notificationEmitter");
const sendEmail = require("./channels/emailChannel");
const sendSMS = require("./channels/smsChannel");
const { sendInApp } = require("./channels/inAppChannel");

// Subscribing MULTIPLE, INDEPENDENT handlers to the SAME event —
// this is the core value of the Observer pattern (Module 25, chapter 02)
notificationEmitter.on("orderPlaced", (user, order) => {
  sendEmail(user, `Your order #${order.id} has been placed!`);
});
notificationEmitter.on("orderPlaced", (user, order) => {
  sendInApp(user, `Order #${order.id} confirmed`);
});

notificationEmitter.on("userRegistered", (user) => {
  sendEmail(user, `Welcome, ${user.name}!`);
  sendSMS(user, `Welcome to our platform, ${user.name}!`);
});
```

**Step 4 — `routes/events.js` (a REST endpoint to trigger events, connecting Module 16's REST concepts to this Pub/Sub system):**
```js
const express = require("express");
const notificationEmitter = require("../events/notificationEmitter");

const router = express.Router();

router.post("/order-placed", (req, res) => {
  const { user, order } = req.body;
  notificationEmitter.emit("orderPlaced", user, order); // Fire the event — all subscribers react!
  res.status(202).json({ message: "Notification event triggered" }); // 202 Accepted — processing is async
});

router.post("/user-registered", (req, res) => {
  const { user } = req.body;
  notificationEmitter.emit("userRegistered", user);
  res.status(202).json({ message: "Notification event triggered" });
});

module.exports = router;
```

**Step 5 — `server.js` (tying it together):**
```js
const express = require("express");
const eventRoutes = require("./routes/events");

const app = express();
app.use(express.json());
app.use("/api/events", eventRoutes);

app.listen(3000, () => console.log("Notification system running on port 3000"));
```

## 5. Explanation

- The core insight: `notificationEmitter.emit("orderPlaced", ...)` **doesn't need to know** which channels are listening — email, in-app, or future channels (Slack? push-notifications?) can be added by simply subscribing to the event, **without modifying the code that triggers it** (Module 26, chapter 01's extensibility principle, applied via Module 25's patterns).
- Each channel-module exports a simple function with a consistent shape `(user, message) => {}` — this consistency is what makes them "interchangeable strategies."
- `202 Accepted` (Module 16, chapter 01) is semantically appropriate here — the request is accepted for processing, but the actual notification-sending may continue asynchronously.

## 6. Debugging

- Agar notifications trigger na hon: verify event-name strings **exactly match** between `.emit()` and `.on()` calls (a common typo-source with string-based event-names).
- Agar server crash ho jaaye unexpectedly: verify `notificationEmitter.on("error", ...)` handler is present (Module 18, chapter 04's critical EventEmitter-error rule).

## 7. Testing

POST to `/api/events/order-placed` and `/api/events/user-registered` with sample `user`/`order` data (via Postman/curl) — verify console-logs show all expected channels firing for each event.

## 8. Improvements

- Add a notification-preferences system (users can opt-out of SMS but keep email).
- Add retry-logic for failed channel-sends (simulate transient failures).

## 9. Advanced Version

Replace the in-memory `EventEmitter` with a real message-queue (like Redis Pub/Sub or RabbitMQ) for a truly distributed, production-scale notification-system that could work across multiple server-instances.

---

**Next:** [05-Ecommerce-Backend](../05-Ecommerce-Backend/README.md)
