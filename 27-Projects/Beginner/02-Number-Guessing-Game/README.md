# Project: Number Guessing Game

**Difficulty:** 🟢 Beginner
**Modules Needed:** 01-05
**Previous:** [01-Calculator](../01-Calculator/README.md)
**Next:** [03-Quiz-App](../03-Quiz-App/README.md)

---

## 1. Requirements

Ek game jisme computer ek random number (1-100) socht hai, aur user ko guess karna hai — game hints deta hai "too high"/"too low".

## 2. Features

- Random number generation
- User input (guess)
- Feedback (higher/lower/correct)
- Attempts counter
- "Play Again" option

## 3. Architecture

```
number-guessing-game/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<!DOCTYPE html>
<html>
<head><title>Guess the Number</title></head>
<body>
  <h1>Guess a number between 1 and 100</h1>
  <input type="number" id="guessInput" min="1" max="100">
  <button id="guessBtn">Guess</button>
  <p id="feedback"></p>
  <p id="attempts"></p>
  <button id="resetBtn" style="display:none">Play Again</button>
  <script src="script.js"></script>
</body>
</html>
```

**Step 2 — JavaScript (Module 04's control flow, Module 05's functions):**
```js
let secretNumber = generateRandomNumber();
let attempts = 0;

function generateRandomNumber() {
  return Math.floor(Math.random() * 100) + 1; // Math.random() returns 0 to <1
}

function checkGuess() {
  const guessInput = document.getElementById("guessInput");
  const feedback = document.getElementById("feedback");
  const guess = Number(guessInput.value); // Module 09's explicit coercion!

  if (!guess || guess < 1 || guess > 100) {
    feedback.textContent = "Please enter a valid number between 1 and 100";
    return;
  }

  attempts++;

  if (guess === secretNumber) {
    feedback.textContent = `🎉 Correct! The number was ${secretNumber}`;
    document.getElementById("attempts").textContent = `Solved in ${attempts} attempts`;
    document.getElementById("guessBtn").disabled = true;
    document.getElementById("resetBtn").style.display = "inline";
  } else if (guess < secretNumber) {
    feedback.textContent = "📈 Too low! Try a higher number";
  } else {
    feedback.textContent = "📉 Too high! Try a lower number";
  }

  guessInput.value = "";
}

function resetGame() {
  secretNumber = generateRandomNumber();
  attempts = 0;
  document.getElementById("feedback").textContent = "";
  document.getElementById("attempts").textContent = "";
  document.getElementById("guessBtn").disabled = false;
  document.getElementById("resetBtn").style.display = "none";
}

document.getElementById("guessBtn").addEventListener("click", checkGuess); // Module 12!
document.getElementById("resetBtn").addEventListener("click", resetGame);
```

## 5. Explanation

- `Math.floor(Math.random() * 100) + 1` — `Math.random()` `[0, 1)` range deta hai, `* 100` isse `[0, 100)` banata hai, `Math.floor()` decimal hataata hai, `+ 1` range ko `[1, 100]` bana deta hai.
- `Number(guessInput.value)` — HTML input values hamesha **strings** hoti hain, explicit conversion (Module 09, chapter 02) zaroori hai comparison ke liye.
- Event listeners (`addEventListener`) Module 12's modern pattern follow karte hain, inline `onclick` ke bajaye.

## 6. Debugging

- Agar comparison kaam nahi kar raha: `console.log(typeof guess)` check karo — agar `"string"` hai, `Number()` conversion miss ho gaya.
- Agar game reset nahi ho raha: verify karo `secretNumber` variable reassign ho raha hai (`let` use hua hai, `const` nahi).

## 7. Testing

Test edge-cases: guess `1` (boundary), guess `100` (boundary), invalid input (jaise letters — should show validation message), guess exactly `secretNumber` on first try.

## 8. Improvements

- Difficulty-levels add karo (range change: 1-50 easy, 1-500 hard).
- LocalStorage (Module 11, chapter 05) use karke "best score" (fewest attempts) persist karo.

## 9. Advanced Version

Ek **two-player mode** banao jaha Player A number socht hai (input field se, hidden), Player B guess karta hai — turn-based hints ke saath.

---

**Next:** [03-Quiz-App](../03-Quiz-App/README.md)
