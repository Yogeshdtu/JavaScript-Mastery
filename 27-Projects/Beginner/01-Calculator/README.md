# Project: Calculator

**Difficulty:** 🟢 Beginner
**Modules Needed:** 01-05 (Foundations, Variables, Operators, Control Flow, Functions)
**Previous:** [27-Projects/00-Projects-Overview.md](../../00-Projects-Overview.md)
**Next:** [02-Number-Guessing-Game](../02-Number-Guessing-Game/README.md)

---

## 1. Requirements

Ek basic calculator banao jo addition, subtraction, multiplication, aur division perform kare, ek clickable button-interface ke saath.

## 2. Features

- Number buttons (0-9)
- Operator buttons (+, -, ×, ÷)
- Equals (=) aur Clear (C) buttons
- Display screen jo current input aur result dikhaye
- Decimal point support

## 3. Architecture

```
calculator/
├── index.html   (structure)
├── style.css    (appearance)
└── script.js    (logic)
```

**Data flow:** Button click → event listener → update state variables → update display.

## 4. Step-by-Step Build Process

**Step 1 — HTML structure:**
```html
<!DOCTYPE html>
<html>
<head>
  <title>Calculator</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="calculator">
    <input type="text" id="display" disabled>
    <div class="buttons">
      <button onclick="clearDisplay()">C</button>
      <button onclick="appendValue('/')">÷</button>
      <button onclick="appendValue('*')">×</button>
      <button onclick="appendValue('7')">7</button>
      <button onclick="appendValue('8')">8</button>
      <button onclick="appendValue('9')">9</button>
      <button onclick="appendValue('-')">-</button>
      <button onclick="appendValue('4')">4</button>
      <button onclick="appendValue('5')">5</button>
      <button onclick="appendValue('6')">6</button>
      <button onclick="appendValue('+')">+</button>
      <button onclick="appendValue('1')">1</button>
      <button onclick="appendValue('2')">2</button>
      <button onclick="appendValue('3')">3</button>
      <button onclick="calculate()">=</button>
      <button onclick="appendValue('0')">0</button>
      <button onclick="appendValue('.')">.</button>
    </div>
  </div>
  <script src="script.js"></script>
</body>
</html>
```

**Step 2 — JavaScript logic (Module 05's functions, Module 09's coercion concepts applied):**
```js
let displayValue = "";

function appendValue(value) {
  displayValue += value;
  updateDisplay();
}

function clearDisplay() {
  displayValue = "";
  updateDisplay();
}

function calculate() {
  try {
    // Note: eval() is used here for simplicity in this beginner project.
    // In production code, you'd write a proper expression parser —
    // eval() can execute arbitrary code, which is a security risk with untrusted input.
    displayValue = eval(displayValue).toString();
  } catch (error) {
    displayValue = "Error";
  }
  updateDisplay();
}

function updateDisplay() {
  document.getElementById("display").value = displayValue;
}
```

**Note on `onclick` attributes:** Is beginner project mein simplicity ke liye inline `onclick` use kiya gaya hai. **✅ Production-quality upgrade:** Module 12, chapter 01 ke `addEventListener()` pattern use karo — har button ko `data-value` attribute do aur ek single event-delegated listener (Module 12, chapter 02) lagao.

## 5. Explanation

- `appendValue()` — display-string mein character add karta hai (Module 05's function-basics).
- `calculate()` — `eval()` string ko JavaScript-expression ki tarah execute karta hai. `try/catch` (Module 01, chapter 06) invalid-expressions (jaise `"5+"`) ko gracefully handle karta hai.
- `updateDisplay()` — DOM ko current state ke saath sync karta hai (preview of Module 11).

## 6. Debugging

- Agar display update nahi ho raha: check karo `id="display"` HTML mein sahi se match karta hai `getElementById` call se.
- Agar calculate() galat result deta hai: `console.log(displayValue)` add karke check karo exact string kya hai calculation se pehle.

## 7. Testing

Manually test karo: `2+3=` → `5`, `10/0=` → `Infinity` (JavaScript's division-by-zero behavior, Module 03 chapter 01 se yaad karo), `5*` (incomplete) `=` → `"Error"`.

## 8. Improvements

- `eval()` ko replace karo ek proper expression-parser se (safer, Module 23 security-mindset).
- Keyboard-input support add karo (Module 12's `keydown` events).
- Operator-chaining ko better handle karo (jaise `5+3+2` ko properly evaluate karna without relying on `eval`).

## 9. Advanced Version

Ek **scientific calculator** banao jisme `Math` object ke functions (sin, cos, sqrt, power) integrate hon, aur calculation-history (Module 06 arrays) maintain ho.

---

**Next:** [02-Number-Guessing-Game](../02-Number-Guessing-Game/README.md)
