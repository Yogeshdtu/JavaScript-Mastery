# Project: Quiz App

**Difficulty:** 🟢 Beginner
**Modules Needed:** 01-07 (adds Arrays, Objects)
**Previous:** [02-Number-Guessing-Game](../02-Number-Guessing-Game/README.md)
**Next:** [04-To-Do-App](../04-To-Do-App/README.md)

---

## 1. Requirements

Multiple-choice quiz application jo questions ek array-of-objects se load kare, score track kare, aur final results dikhaye.

## 2. Features

- Questions array (Module 06) with options aur correct answer
- One question at a time display
- Score tracking
- Progress indicator ("Question 3 of 10")
- Final score summary

## 3. Architecture

```
quiz-app/
├── index.html
├── style.css
└── script.js  (contains questions data + logic)
```

## 4. Step-by-Step Build Process

**Step 1 — Data structure (Module 07's objects, Module 06's arrays):**
```js
const questions = [
  {
    question: "What is the output of typeof null?",
    options: ["'null'", "'object'", "'undefined'", "'number'"],
    correctIndex: 1
  },
  {
    question: "Which method adds an element to the end of an array?",
    options: ["shift()", "unshift()", "push()", "pop()"],
    correctIndex: 2
  },
  {
    question: "What does '===' check that '==' doesn't?",
    options: ["Nothing, they're the same", "Value only", "Type and value", "Only type"],
    correctIndex: 2
  }
];

let currentQuestionIndex = 0;
let score = 0;
```

**Step 2 — HTML:**
```html
<div id="quiz-container">
  <p id="progress"></p>
  <h2 id="question"></h2>
  <div id="options"></div>
  <p id="feedback"></p>
  <button id="nextBtn" style="display:none">Next</button>
</div>
<div id="results" style="display:none">
  <h2>Quiz Complete!</h2>
  <p id="finalScore"></p>
</div>
```

**Step 3 — Rendering logic (Module 06's array iteration, Module 11's DOM manipulation):**
```js
function loadQuestion() {
  const q = questions[currentQuestionIndex];
  document.getElementById("progress").textContent =
    `Question ${currentQuestionIndex + 1} of ${questions.length}`;
  document.getElementById("question").textContent = q.question;

  const optionsContainer = document.getElementById("options");
  optionsContainer.innerHTML = ""; // Clear previous options

  q.options.forEach((option, index) => { // Module 06's forEach!
    const button = document.createElement("button");
    button.textContent = option;
    button.addEventListener("click", () => selectAnswer(index));
    optionsContainer.appendChild(button);
  });

  document.getElementById("feedback").textContent = "";
  document.getElementById("nextBtn").style.display = "none";
}

function selectAnswer(selectedIndex) {
  const q = questions[currentQuestionIndex];
  const feedback = document.getElementById("feedback");

  if (selectedIndex === q.correctIndex) {
    score++;
    feedback.textContent = "✅ Correct!";
  } else {
    feedback.textContent = `❌ Wrong! Correct answer: ${q.options[q.correctIndex]}`;
  }

  // Disable all option buttons after answering
  document.querySelectorAll("#options button").forEach(btn => btn.disabled = true);
  document.getElementById("nextBtn").style.display = "inline";
}

function nextQuestion() {
  currentQuestionIndex++;
  if (currentQuestionIndex < questions.length) {
    loadQuestion();
  } else {
    showResults();
  }
}

function showResults() {
  document.getElementById("quiz-container").style.display = "none";
  document.getElementById("results").style.display = "block";
  document.getElementById("finalScore").textContent =
    `You scored ${score} out of ${questions.length}`;
}

document.getElementById("nextBtn").addEventListener("click", nextQuestion);
loadQuestion(); // Initialize
```

## 5. Explanation

- Questions ko **data (array of objects)** ki tarah store karna, hardcoded HTML ki tarah nahi — ye separation-of-concerns hai (Module 26, chapter 01's principles).
- `forEach` (Module 06, chapter 04) dynamically buttons generate karta hai — naya question add karne ke liye sirf `questions` array update karna padta hai, HTML nahi.
- Closures (Module 08) — `selectAnswer(index)` arrow function mein `index` ko capture karta hai har button ke liye correctly.

## 6. Debugging

- Agar options click karne pe kuch na ho: verify karo `addEventListener` sahi se attach hua har button pe (loop ke andar closures common issue point hain — Module 08, chapter 06 ke `var`-loop-bug se related, though `forEach`+arrow-function is safe).
- Agar score galat count ho raha: `console.log(selectedIndex, q.correctIndex)` add karo comparison verify karne ke liye.

## 7. Testing

Poora quiz complete karo different combinations ke saath (sab sahi, sab galat, mixed) — verify karo final score accurately match karta hai.

## 8. Improvements

- Timer add karo per-question (Module 13's `setTimeout`).
- LocalStorage (Module 11, chapter 05) se high-scores persist karo.

## 9. Advanced Version

Quiz-questions ko ek external JSON file/API se fetch karo (Module 16's Fetch API) instead of hardcoded array, aur categories/difficulty-levels add karo.

---

**Next:** [04-To-Do-App](../04-To-Do-App/README.md)
