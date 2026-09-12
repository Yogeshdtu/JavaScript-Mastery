# Project: Digital Clock

**Difficulty:** 🟢 Beginner
**Modules Needed:** 01-05, 11 (adds Date object, Timers, DOM)
**Previous:** [04-To-Do-App](../04-To-Do-App/README.md)
**Next:** [06-Unit-Converter](../06-Unit-Converter/README.md)

---

## 1. Requirements

Ek live-updating digital clock jo current time (HH:MM:SS) aur date display kare, har second update hote hue.

## 2. Features

- Live time display (updates every second)
- 12-hour/24-hour format toggle
- Current date display
- Leading zeros (e.g., "09" not "9")

## 3. Architecture

```
digital-clock/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<div id="clock-container">
  <h1 id="time"></h1>
  <p id="date"></p>
  <button id="formatToggle">Switch to 12-hour</button>
</div>
```

**Step 2 — JavaScript (Module 13's setInterval, Date object):**
```js
let is24Hour = true;

function updateClock() {
  const now = new Date(); // Built-in Date object
  let hours = now.getHours();
  const minutes = now.getMinutes();
  const seconds = now.getSeconds();

  let period = "";
  if (!is24Hour) {
    period = hours >= 12 ? "PM" : "AM";
    hours = hours % 12 || 12; // Convert 0/13-23 to 12-hour format (0 becomes 12)
  }

  // Padding with leading zeros (Module 01's String methods)
  const formattedTime =
    `${String(hours).padStart(2, "0")}:${String(minutes).padStart(2, "0")}:${String(seconds).padStart(2, "0")} ${period}`;

  document.getElementById("time").textContent = formattedTime.trim();

  const dateOptions = { weekday: "long", year: "numeric", month: "long", day: "numeric" };
  document.getElementById("date").textContent = now.toLocaleDateString(undefined, dateOptions);
}

document.getElementById("formatToggle").addEventListener("click", () => {
  is24Hour = !is24Hour;
  document.getElementById("formatToggle").textContent =
    is24Hour ? "Switch to 12-hour" : "Switch to 24-hour";
  updateClock(); // Update immediately, don't wait for the next tick
});

updateClock(); // Show immediately on load
setInterval(updateClock, 1000); // Then update every second (Module 13, chapter 01!)
```

## 5. Explanation

- `setInterval(updateClock, 1000)` (Module 13, chapter 01) `updateClock` ko har 1000ms (1 second) pe call karta hai — ye ek recurring async operation hai, Web APIs ke through handle hoti hai.
- `String(hours).padStart(2, "0")` ensure karta hai single-digit numbers (jaise `9`) `"09"` ban jaayein — ek common formatting-pattern.
- `now.getHours() % 12 || 12` — ek clever trick: agar `hours % 12` `0` hai (jo 12 AM/PM ke liye hota hai), `|| 12` (Module 03, chapter 03's `||` operator) fallback deta hai `12`.

## 6. Debugging

- Agar clock update nahi ho raha: verify `setInterval` call ki gayi hai (na ki galti se `setTimeout`, jo sirf ek baar chalta).
- Agar format-toggle turant reflect nahi hota: verify `updateClock()` explicitly call kiya gaya hai toggle ke andar (warna 1 second tak wait karna padega next auto-update ka).

## 7. Testing

Browser mein test karo — verify seconds smoothly increment ho rahe hain bina "jumps"/"skips" ke, aur midnight (00:00:00) ke aas-paas 12-hour format sahi se "12:00:00 AM" dikhaye.

## 8. Improvements

- Multiple timezone-support add karo (`Intl.DateTimeFormat` API).
- Alarm-functionality add karo (specific time pe notification).

## 9. Advanced Version

Ek **Pomodoro timer** banao (25-min work, 5-min break cycles) using the same `setInterval` foundation, with start/pause/reset controls (Module 22's debounce concepts could apply to button-spam prevention).

---

**Next:** [06-Unit-Converter](../06-Unit-Converter/README.md)
