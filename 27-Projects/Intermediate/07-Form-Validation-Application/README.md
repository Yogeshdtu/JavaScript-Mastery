# Project: Form Validation Application

**Difficulty:** 🟡 Intermediate
**Modules Needed:** 11 (Forms), 03 (Operators/regex-adjacent concepts)
**Previous:** [06-Dashboard](../06-Dashboard/README.md)
**Next:** [Advanced/01-Authentication-System](../../Advanced/01-Authentication-System/README.md)

---

## 1. Requirements

Ek signup-form jo comprehensive client-side validation kare — real-time feedback ke saath, meaningful error-messages ke saath.

## 2. Features

- Real-time (on-input) validation feedback
- Multiple field-types: name, email, password, confirm-password, age
- Password-strength indicator
- Submit sirf tab enable ho jab saara form valid ho

## 3. Architecture

```
form-validation-app/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<form id="signupForm" novalidate>
  <div class="field">
    <input type="text" id="name" placeholder="Full Name">
    <span class="error" id="nameError"></span>
  </div>
  <div class="field">
    <input type="email" id="email" placeholder="Email">
    <span class="error" id="emailError"></span>
  </div>
  <div class="field">
    <input type="password" id="password" placeholder="Password">
    <div id="strengthBar"></div>
    <span class="error" id="passwordError"></span>
  </div>
  <div class="field">
    <input type="password" id="confirmPassword" placeholder="Confirm Password">
    <span class="error" id="confirmError"></span>
  </div>
  <div class="field">
    <input type="number" id="age" placeholder="Age">
    <span class="error" id="ageError"></span>
  </div>
  <button type="submit" id="submitBtn" disabled>Sign Up</button>
</form>
```
`novalidate` disables the browser's built-in validation-popups so we can show our own custom, styled error-messages instead (Module 11, chapter 04's Constraint Validation API is still used internally, just not its default UI).

**Step 2 — Validation functions (each field, single-responsibility — Module 26, chapter 01):**
```js
const validators = {
  name(value) {
    if (!value.trim()) return "Name is required";
    if (value.trim().length < 2) return "Name must be at least 2 characters";
    return null; // null means "valid"
  },
  email(value) {
    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/; // Basic email-shape check
    if (!value) return "Email is required";
    if (!emailPattern.test(value)) return "Please enter a valid email";
    return null;
  },
  password(value) {
    if (!value) return "Password is required";
    if (value.length < 8) return "Password must be at least 8 characters";
    if (!/[0-9]/.test(value)) return "Password must contain a number";
    if (!/[A-Z]/.test(value)) return "Password must contain an uppercase letter";
    return null;
  },
  confirmPassword(value, allValues) {
    if (value !== allValues.password) return "Passwords do not match"; // Cross-field validation!
    return null;
  },
  age(value) {
    const age = Number(value); // Module 09's explicit coercion
    if (!value) return "Age is required";
    if (age < 13) return "You must be at least 13 years old";
    if (age > 120) return "Please enter a valid age";
    return null;
  }
};
```

**Step 3 — Wiring real-time validation and password-strength (Module 11, chapter 04):**
```js
function getAllValues() {
  return {
    name: document.getElementById("name").value,
    email: document.getElementById("email").value,
    password: document.getElementById("password").value,
    confirmPassword: document.getElementById("confirmPassword").value,
    age: document.getElementById("age").value
  };
}

function validateField(fieldName) {
  const input = document.getElementById(fieldName);
  const errorSpan = document.getElementById(`${fieldName}Error`);
  const error = validators[fieldName](input.value, getAllValues());

  errorSpan.textContent = error || "";
  input.classList.toggle("invalid", !!error); // Module 03, ch 04's !! for boolean coercion
  return error === null;
}

function validateAllFields() {
  const fieldNames = Object.keys(validators); // Module 07, chapter 03!
  const results = fieldNames.map(validateField); // Module 06, chapter 03!
  return results.every(isValid => isValid); // Module 06, chapter 03's every()!
}

function updatePasswordStrength(password) {
  let strength = 0;
  if (password.length >= 8) strength++;
  if (/[0-9]/.test(password)) strength++;
  if (/[A-Z]/.test(password)) strength++;
  if (/[^A-Za-z0-9]/.test(password)) strength++;

  const strengthBar = document.getElementById("strengthBar");
  const levels = ["Weak", "Fair", "Good", "Strong"];
  strengthBar.textContent = password ? levels[strength - 1] || "Weak" : "";
  strengthBar.className = `strength-${strength}`;
}

// Attach real-time validation to every field
Object.keys(validators).forEach(fieldName => {
  document.getElementById(fieldName).addEventListener("input", () => {
    validateField(fieldName);
    if (fieldName === "password") {
      updatePasswordStrength(document.getElementById("password").value);
      validateField("confirmPassword"); // Re-check confirm-password when password changes
    }
    document.getElementById("submitBtn").disabled = !validateAllFields();
  });
});

document.getElementById("signupForm").addEventListener("submit", (event) => {
  event.preventDefault(); // Module 11, chapter 04!
  if (validateAllFields()) {
    console.log("Form is valid! Submitting:", getAllValues());
    // In a real app: send to backend (Module 16's fetch, Module 19's API)
  }
});
```

## 5. Explanation

- Each validator is a **pure function** (Module 05, chapter 07) that takes a value and returns either `null` (valid) or an error-message string — this makes validators easy to test independently (Module 21).
- `confirmPassword`'s validator demonstrates **cross-field validation** — it needs access to `password`'s value too, passed via the `allValues` parameter.
- `.every()` (Module 06, chapter 03) elegantly checks "are ALL fields valid" from an array of individual results.

## 6. Debugging

- Agar submit-button kabhi enable na ho: `console.log` add karo `validateAllFields()` ke andar dekhne ke liye kaunsa specific field fail ho raha hai.
- Agar password-match-check stale lage: verify `validateField("confirmPassword")` explicitly re-triggered ho raha hai jab `password` field change hoti hai (cross-field dependency).

## 7. Testing

Test each validation-rule individually (too-short name, invalid email formats, weak passwords, mismatched confirm-password, out-of-range age) — verify appropriate error-messages show and submit stays disabled until ALL are fixed.

## 8. Improvements

- Add debounced validation (Module 22, chapter 03) for fields where instant-feedback feels too aggressive.
- Add a "show password" toggle button.

## 9. Advanced Version

Connect this form to a real backend (Module 19, chapter 03's registration-endpoint) with **server-side validation too** (Module 16, chapter 04's principle: client-side validation is UX, server-side is security) — showing server-returned errors (like "email already registered") in the same UI.

---

**Intermediate Projects Complete!** Next: [Advanced/01-Authentication-System](../../Advanced/01-Authentication-System/README.md)
