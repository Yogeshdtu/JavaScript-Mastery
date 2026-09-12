# Project: Unit Converter

**Difficulty:** 🟢 Beginner
**Modules Needed:** 01-05, 11
**Previous:** [05-Digital-Clock](../05-Digital-Clock/README.md)
**Next:** [Intermediate/01-Weather-App](../../Intermediate/01-Weather-App/README.md)

---

## 1. Requirements

Ek converter jo length, weight, aur temperature units ke beech convert kare (jaise km↔miles, kg↔lbs, Celsius↔Fahrenheit).

## 2. Features

- Category selection (Length/Weight/Temperature)
- Unit selection (dropdowns) for "from" and "to"
- Real-time conversion as user types
- Swap-units button

## 3. Architecture

```
unit-converter/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — Conversion logic as data (Module 07's objects):**
```js
const conversionFactors = {
  length: {
    meters: 1,
    kilometers: 1000,
    miles: 1609.34,
    feet: 0.3048
  },
  weight: {
    grams: 1,
    kilograms: 1000,
    pounds: 453.592
  }
};

function convert(category, value, fromUnit, toUnit) {
  if (category === "temperature") {
    return convertTemperature(value, fromUnit, toUnit);
  }
  const factors = conversionFactors[category];
  const baseValue = value * factors[fromUnit]; // Convert to base unit first
  return baseValue / factors[toUnit];            // Then to target unit
}

function convertTemperature(value, fromUnit, toUnit) {
  let celsius;
  // Convert input to Celsius first (as a common "base")
  if (fromUnit === "celsius") celsius = value;
  else if (fromUnit === "fahrenheit") celsius = (value - 32) * (5 / 9);
  else if (fromUnit === "kelvin") celsius = value - 273.15;

  // Then convert Celsius to the target unit
  if (toUnit === "celsius") return celsius;
  if (toUnit === "fahrenheit") return celsius * (9 / 5) + 32;
  if (toUnit === "kelvin") return celsius + 273.15;
}
```

**Step 2 — HTML:**
```html
<div id="converter">
  <select id="category">
    <option value="length">Length</option>
    <option value="weight">Weight</option>
    <option value="temperature">Temperature</option>
  </select>

  <input type="number" id="inputValue" value="0">
  <select id="fromUnit"></select>
  <span>to</span>
  <select id="toUnit"></select>
  <button id="swapBtn">⇄ Swap</button>

  <p id="result"></p>
</div>
```

**Step 3 — Wiring it together (Module 11's dynamic DOM updates):**
```js
const temperatureUnits = { celsius: 1, fahrenheit: 1, kelvin: 1 }; // Just for populating dropdowns

function populateUnitDropdowns() {
  const category = document.getElementById("category").value;
  const units = category === "temperature" ? temperatureUnits : conversionFactors[category];
  const unitNames = Object.keys(units); // Module 07, chapter 03!

  const fromSelect = document.getElementById("fromUnit");
  const toSelect = document.getElementById("toUnit");
  fromSelect.innerHTML = toSelect.innerHTML = unitNames
    .map(unit => `<option value="${unit}">${unit}</option>`)
    .join("");

  toSelect.selectedIndex = 1 % unitNames.length; // Default to a different unit than "from"
  performConversion();
}

function performConversion() {
  const category = document.getElementById("category").value;
  const value = Number(document.getElementById("inputValue").value); // Module 09's coercion!
  const fromUnit = document.getElementById("fromUnit").value;
  const toUnit = document.getElementById("toUnit").value;

  const result = convert(category, value, fromUnit, toUnit);
  document.getElementById("result").textContent =
    `${value} ${fromUnit} = ${result.toFixed(2)} ${toUnit}`;
}

function swapUnits() {
  const fromSelect = document.getElementById("fromUnit");
  const toSelect = document.getElementById("toUnit");
  [fromSelect.value, toSelect.value] = [toSelect.value, fromSelect.value]; // Array destructuring swap (Module 07, ch 02)!
  performConversion();
}

document.getElementById("category").addEventListener("change", populateUnitDropdowns);
document.getElementById("inputValue").addEventListener("input", performConversion); // Real-time!
document.getElementById("fromUnit").addEventListener("change", performConversion);
document.getElementById("toUnit").addEventListener("change", performConversion);
document.getElementById("swapBtn").addEventListener("click", swapUnits);

populateUnitDropdowns(); // Initialize
```

## 5. Explanation

- Conversions ko "convert to base unit, then to target" approach se handle kiya gaya hai — ye O(1) conversions har pair ke beech directly define karne se zyada maintainable hai (Module 26, chapter 01's DRY-principle).
- `[a, b] = [b, a]` — array-destructuring (Module 07, chapter 02) ek elegant "swap" pattern hai bina temporary variable ke.
- `input` event (not just `change`) real-time updates deta hai jaise user types.

## 6. Debugging

- Agar temperature-conversion galat result de: manually formula verify karo (`(F - 32) * 5/9 = C`), aur `console.log` intermediate `celsius` value.
- Agar dropdown-options update nahi ho rahe category-change pe: verify `populateUnitDropdowns` `change` event pe correctly attached hai.

## 7. Testing

Known conversions verify karo: `0°C = 32°F`, `100°C = 212°F`, `1 kilometer = 1000 meters`, `1 kilogram = 2.20 pounds` (approximately).

## 8. Improvements

- Volume/Area/Speed categories add karo.
- Input-validation add karo (negative Kelvin invalid hai — absolute zero se neeche nahi ja sakte).

## 9. Advanced Version

Ek **currency converter** banao jo real exchange-rates ek API se fetch kare (Module 16's Fetch API), rates ko cache kare (Module 22, chapter 04), aur historical-rate-trends dikhaye.

---

**Beginner Projects Complete!** Next: [Intermediate/01-Weather-App](../../Intermediate/01-Weather-App/README.md)
