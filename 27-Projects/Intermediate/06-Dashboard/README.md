# Project: Dashboard

**Difficulty:** 🟡 Intermediate
**Modules Needed:** 06-07, 11-12
**Previous:** [05-Shopping-Cart](../05-Shopping-Cart/README.md)
**Next:** [07-Form-Validation-Application](../07-Form-Validation-Application/README.md)

---

## 1. Requirements

Ek data-dashboard jo multiple "widgets" (stat-cards, a simple bar-chart, a recent-activity list) ek saath ek page pe dikhaye, sample-data se driven.

## 2. Features

- Stat-cards (total users, revenue, orders — computed from data)
- Simple CSS-based bar-chart (no external library)
- Recent-activity feed
- Filter by date-range (basic)

## 3. Architecture

```
dashboard/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — Sample data (simulating what would come from a real backend, Module 20):**
```js
const salesData = [
  { date: "2026-01-01", amount: 4500, category: "Electronics" },
  { date: "2026-01-02", amount: 3200, category: "Clothing" },
  { date: "2026-01-03", amount: 5100, category: "Electronics" },
  { date: "2026-01-04", amount: 2800, category: "Groceries" },
  { date: "2026-01-05", amount: 6200, category: "Electronics" },
  { date: "2026-01-06", amount: 3900, category: "Clothing" },
  { date: "2026-01-07", amount: 4700, category: "Groceries" }
];
```

**Step 2 — Computing stats (Module 06's array methods, especially reduce):**
```js
function getTotalRevenue(data) {
  return data.reduce((sum, sale) => sum + sale.amount, 0); // Module 06, chapter 03!
}

function getAverageOrderValue(data) {
  return data.length === 0 ? 0 : getTotalRevenue(data) / data.length;
}

function getRevenueByCategory(data) {
  return data.reduce((breakdown, sale) => { // Grouping pattern, revisited from Expense Tracker!
    breakdown[sale.category] = (breakdown[sale.category] || 0) + sale.amount;
    return breakdown;
  }, {});
}

function getTopCategory(data) {
  const breakdown = getRevenueByCategory(data);
  return Object.entries(breakdown) // Module 07, chapter 03!
    .sort((a, b) => b[1] - a[1])[0]; // Module 06, chapter 02's sort with compare function!
}
```

**Step 3 — Rendering stat-cards:**
```js
function renderStats(data) {
  document.getElementById("totalRevenue").textContent = `₹${getTotalRevenue(data).toLocaleString()}`;
  document.getElementById("avgOrder").textContent = `₹${getAverageOrderValue(data).toFixed(2)}`;
  document.getElementById("orderCount").textContent = data.length;

  const [topCategory, topAmount] = getTopCategory(data) || ["N/A", 0]; // Array destructuring!
  document.getElementById("topCategory").textContent = `${topCategory} (₹${topAmount})`;
}
```

**Step 4 — A simple CSS-based bar-chart (no external charting library needed):**
```js
function renderBarChart(data) {
  const breakdown = getRevenueByCategory(data);
  const maxValue = Math.max(...Object.values(breakdown)); // Module 03, ch 05's spread with Math.max!

  const chartContainer = document.getElementById("barChart");
  chartContainer.innerHTML = Object.entries(breakdown).map(([category, amount]) => {
    const heightPercent = (amount / maxValue) * 100;
    return `
      <div class="bar-wrapper">
        <div class="bar" style="height: ${heightPercent}%"></div>
        <span class="bar-label">${category}</span>
        <span class="bar-value">₹${amount}</span>
      </div>
    `;
  }).join("");
}
```
```css
/* style.css excerpt */
#barChart { display: flex; align-items: flex-end; gap: 16px; height: 200px; }
.bar-wrapper { display: flex; flex-direction: column-reverse; align-items: center; }
.bar { width: 40px; background: #4a90d9; border-radius: 4px 4px 0 0; }
```

**Step 5 — Recent activity and date-filtering:**
```js
function renderRecentActivity(data) {
  const recent = [...data].reverse().slice(0, 5); // Copy first (Module 06, ch 04), don't mutate original!
  document.getElementById("activityFeed").innerHTML = recent.map(sale => `
    <li>${sale.date}: ₹${sale.amount} (${sale.category})</li>
  `).join("");
}

function filterByDateRange(data, startDate, endDate) {
  return data.filter(sale => sale.date >= startDate && sale.date <= endDate); // String comparison works for ISO dates!
}

function updateDashboard(data) {
  renderStats(data);
  renderBarChart(data);
  renderRecentActivity(data);
}

document.getElementById("applyFilterBtn").addEventListener("click", () => {
  const start = document.getElementById("startDate").value;
  const end = document.getElementById("endDate").value;
  const filtered = (start && end) ? filterByDateRange(salesData, start, end) : salesData;
  updateDashboard(filtered);
});

updateDashboard(salesData); // Initial render with all data
```

## 5. Explanation

- ISO-format dates (`"2026-01-01"`) can be **directly string-compared** (`>=`, `<=`) because their lexicographic order matches chronological order — a handy trick that avoids needing full `Date` object parsing for simple range checks.
- `Math.max(...Object.values(breakdown))` (Module 03, chapter 05's spread) finds the largest category-total, used to scale the bar-chart proportionally.
- `[...data].reverse()` (Module 06, chapter 04) creates a copy before reversing — reversing the original `salesData` directly would mutate shared state unexpectedly.

## 6. Debugging

- Agar bar-chart heights sab same dikhein: verify `maxValue` correctly calculate ho raha hai, aur `heightPercent` calculation check karo.
- Agar date-filter kuch bhi return na kare: verify date-format consistency (`"2026-01-01"` format everywhere, no mixed formats).

## 7. Testing

Apply various date-ranges (including one with no matching data — should show empty/zero states gracefully), verify stat-cards update correctly when filtered.

## 8. Improvements

- Add more chart-types (pie-chart using CSS `conic-gradient`).
- Add sorting-options for the activity-feed.

## 9. Advanced Version

Connect to a real backend (Module 19-20) that aggregates actual data, and add auto-refresh (Module 13's `setInterval`) to simulate a "live" dashboard.

---

**Next:** [07-Form-Validation-Application](../07-Form-Validation-Application/README.md)
