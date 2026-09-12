# Project: Expense Tracker

**Difficulty:** 🟡 Intermediate
**Modules Needed:** 06-07, 09, 11
**Previous:** [01-Weather-App](../01-Weather-App/README.md)
**Next:** [03-Notes-Application](../03-Notes-Application/README.md)

---

## 1. Requirements

Ek expense-tracker jo income/expenses record kare, category-wise breakdown dikhaye, aur running-balance maintain kare.

## 2. Features

- Add transaction (description, amount, type: income/expense, category)
- Running balance (total income - total expenses)
- Category-wise totals
- Delete transaction
- Persist in localStorage

## 3. Architecture

```
expense-tracker/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<div id="app">
  <div id="balance-section">
    <h2>Balance: <span id="balance">₹0</span></h2>
    <p>Income: <span id="totalIncome">₹0</span> | Expenses: <span id="totalExpense">₹0</span></p>
  </div>

  <form id="transactionForm">
    <input type="text" id="description" placeholder="Description" required>
    <input type="number" id="amount" placeholder="Amount" required>
    <select id="type">
      <option value="income">Income</option>
      <option value="expense">Expense</option>
    </select>
    <select id="category">
      <option value="food">Food</option>
      <option value="transport">Transport</option>
      <option value="salary">Salary</option>
      <option value="other">Other</option>
    </select>
    <button type="submit">Add</button>
  </form>

  <ul id="transactionList"></ul>
  <div id="categoryBreakdown"></div>
</div>
```

**Step 2 — State and calculations (Module 06, chapter 03's reduce — core to this project):**
```js
let transactions = JSON.parse(localStorage.getItem("transactions")) || [];

function saveTransactions() {
  localStorage.setItem("transactions", JSON.stringify(transactions));
}

function calculateBalance() {
  return transactions.reduce((total, t) => {
    return t.type === "income" ? total + t.amount : total - t.amount;
  }, 0);
}

function calculateTotalByType(type) {
  return transactions
    .filter(t => t.type === type) // Module 06, chapter 03
    .reduce((sum, t) => sum + t.amount, 0);
}

function calculateCategoryBreakdown() {
  // Grouping pattern using reduce — a very common real-world use-case!
  return transactions
    .filter(t => t.type === "expense")
    .reduce((breakdown, t) => {
      breakdown[t.category] = (breakdown[t.category] || 0) + t.amount; // Module 03, ch 03's || for defaults!
      return breakdown;
    }, {});
}
```

**Step 3 — CRUD and rendering:**
```js
function addTransaction(description, amount, type, category) {
  transactions.push({
    id: Date.now(),
    description,
    amount: Number(amount), // Module 09's explicit coercion — form values are always strings!
    type,
    category
  });
  saveTransactions();
  render();
}

function deleteTransaction(id) {
  transactions = transactions.filter(t => t.id !== id);
  saveTransactions();
  render();
}

function render() {
  document.getElementById("balance").textContent = `₹${calculateBalance().toFixed(2)}`;
  document.getElementById("totalIncome").textContent = `₹${calculateTotalByType("income").toFixed(2)}`;
  document.getElementById("totalExpense").textContent = `₹${calculateTotalByType("expense").toFixed(2)}`;

  const list = document.getElementById("transactionList");
  list.innerHTML = transactions.map(t => `
    <li class="${t.type}">
      ${t.description} (${t.category}): ${t.type === "expense" ? "-" : "+"}₹${t.amount}
      <button data-id="${t.id}" class="delete-btn">✗</button>
    </li>
  `).join("");

  const breakdown = calculateCategoryBreakdown();
  document.getElementById("categoryBreakdown").innerHTML = Object.entries(breakdown) // Module 07, ch 03!
    .map(([category, total]) => `<p>${category}: ₹${total.toFixed(2)}</p>`)
    .join("");
}

document.getElementById("transactionList").addEventListener("click", (event) => {
  if (event.target.classList.contains("delete-btn")) {
    deleteTransaction(Number(event.target.dataset.id));
  }
});

document.getElementById("transactionForm").addEventListener("submit", (event) => {
  event.preventDefault();
  const description = document.getElementById("description").value;
  const amount = document.getElementById("amount").value;
  const type = document.getElementById("type").value;
  const category = document.getElementById("category").value;

  addTransaction(description, amount, type, category);
  event.target.reset();
});

render();
```

## 5. Explanation

- `reduce()` (Module 06, chapter 03) is used **three different ways** here — sum-calculation, filtered-sum, aur grouping-into-an-object — showing its versatility as the "swiss army knife" of array methods.
- `breakdown[t.category] = (breakdown[t.category] || 0) + t.amount` — a classic "accumulate into an object" pattern, using `||` (Module 03, chapter 03) for the "not yet initialized" default.
- Event delegation (Module 12, chapter 02) handles delete-buttons for dynamically-rendered list-items.

## 6. Debugging

- Agar balance galat calculate ho: `console.log(transactions)` se verify karo `amount` fields actually **numbers** hain, strings nahi (Module 09's coercion-gotcha).
- Agar category-breakdown incomplete lage: verify `filter(t => t.type === "expense")` sahi transactions include kar raha hai.

## 7. Testing

Add mix of income/expense transactions across categories, verify balance = total-income - total-expense, aur category-totals sum karke total-expense ke barabar aana chahiye.

## 8. Improvements

- Date-range filtering add karo.
- Simple bar-chart visualization (canvas ya CSS-based bars) category-breakdown ke liye.

## 9. Advanced Version

Backend-integration (Module 19-20) — transactions ko database mein store karo instead of localStorage, multi-user support ke saath (Module 19, chapter 03's authentication).

---

**Next:** [03-Notes-Application](../03-Notes-Application/README.md)
