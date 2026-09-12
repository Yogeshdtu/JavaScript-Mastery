# Project: Weather App

**Difficulty:** 🟡 Intermediate
**Modules Needed:** 13-16 (Async JavaScript, Fetch API)
**Previous:** [Beginner/06-Unit-Converter](../../Beginner/06-Unit-Converter/README.md)
**Next:** [02-Expense-Tracker](../02-Expense-Tracker/README.md)

---

## 1. Requirements

Ek weather-application jo city-name input par real weather-data ek external API se fetch kare aur display kare.

## 2. Features

- City-search input
- Current temperature, condition, humidity display
- Loading-state aur error-handling
- Search-history (recent searches)

## 3. Architecture

```
weather-app/
├── index.html
├── style.css
└── script.js
```

**Note:** Is project ke liye ek free weather-API (jaise OpenWeatherMap) se API-key chahiye hogi — signup free hai. API-key ko client-side code mein directly expose karna beginner-projects ke liye acceptable hai (learning-purpose), lekin production apps mein Module 18 chapter 03 ke environment-variables + backend-proxy pattern use karna chahiye (Module 23, chapter 04's "never hardcode secrets" principle).

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<div id="weather-app">
  <form id="searchForm">
    <input type="text" id="cityInput" placeholder="Enter city name" required>
    <button type="submit">Search</button>
  </form>
  <div id="loadingIndicator" style="display:none">Loading...</div>
  <div id="errorMessage" style="display:none"></div>
  <div id="weatherResult" style="display:none">
    <h2 id="cityName"></h2>
    <p id="temperature"></p>
    <p id="condition"></p>
    <p id="humidity"></p>
  </div>
</div>
```

**Step 2 — Fetching weather data (Module 16, chapters 01-02):**
```js
const API_KEY = "YOUR_API_KEY_HERE"; // Replace with your actual key
const BASE_URL = "https://api.openweathermap.org/data/2.5/weather";

async function fetchWeather(city) {
  const url = `${BASE_URL}?q=${encodeURIComponent(city)}&appid=${API_KEY}&units=metric`;

  const response = await fetch(url);

  if (!response.ok) { // Module 16, chapter 02's CRITICAL gotcha!
    if (response.status === 404) {
      throw new Error("City not found. Please check the spelling.");
    }
    throw new Error(`Weather service error: ${response.status}`);
  }

  return response.json();
}
```
`encodeURIComponent()` city-name ko safely URL mein embed karta hai (special characters, spaces properly escape karta hai).

**Step 3 — Orchestrating the UI states (Module 14's async/await, Module 14 chapter 02's error handling):**
```js
async function handleSearch(city) {
  const loading = document.getElementById("loadingIndicator");
  const errorDiv = document.getElementById("errorMessage");
  const resultDiv = document.getElementById("weatherResult");

  loading.style.display = "block";
  errorDiv.style.display = "none";
  resultDiv.style.display = "none";

  try {
    const data = await fetchWeather(city);
    displayWeather(data);
    saveToHistory(city);
  } catch (error) {
    errorDiv.textContent = error.message;
    errorDiv.style.display = "block";
  } finally {
    loading.style.display = "none"; // Always hide loading, success or failure
  }
}

function displayWeather(data) {
  document.getElementById("cityName").textContent = `${data.name}, ${data.sys.country}`;
  document.getElementById("temperature").textContent = `${Math.round(data.main.temp)}°C`;
  document.getElementById("condition").textContent = data.weather[0].description;
  document.getElementById("humidity").textContent = `Humidity: ${data.main.humidity}%`;
  document.getElementById("weatherResult").style.display = "block";
}

function saveToHistory(city) {
  let history = JSON.parse(localStorage.getItem("weatherHistory")) || []; // Module 11, ch 05!
  history = [city, ...history.filter(c => c !== city)].slice(0, 5); // Keep last 5, no duplicates
  localStorage.setItem("weatherHistory", JSON.stringify(history));
}

document.getElementById("searchForm").addEventListener("submit", (event) => {
  event.preventDefault();
  const city = document.getElementById("cityInput").value.trim();
  if (city) handleSearch(city);
});
```

## 5. Explanation

- `try/catch/finally` (Module 14, chapter 02) ensures loading-indicator **always** hides, chahe request succeed ho ya fail ho.
- `response.ok` check (Module 16, chapter 02) explicitly handles the "fetch resolves even on 404" gotcha.
- Search-history localStorage pattern reuses Module 27's To-Do-App JSON-persistence approach.

## 6. Debugging

- Agar "Failed to fetch" error aaye: check karo API-key valid hai, aur internet-connection active hai (Module 13, chapter 01's network-dependency).
- Agar city milta hi nahi hamesha: verify `encodeURIComponent()` use ho raha hai spaces/special-characters wale city-names ke liye (jaise "New York").

## 7. Testing

Test with valid cities, an intentionally-misspelled city (should show 404-error message), aur empty-input (form's `required` attribute should prevent submission).

## 8. Improvements

- Debounced auto-search (Module 22, chapter 03) as user types, instead of requiring form-submit.
- Geolocation-based "current location weather" (browser's Geolocation API).

## 9. Advanced Version

5-day forecast add karo (different API endpoint), aur temperature-unit toggle (Celsius/Fahrenheit, reusing Module 27's Unit-Converter logic).

---

**Next:** [02-Expense-Tracker](../02-Expense-Tracker/README.md)
