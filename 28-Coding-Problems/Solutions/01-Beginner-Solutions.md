# Beginner Solutions

**Back to problems:** [01-Beginner/01-Problems.md](../01-Beginner/01-Problems.md)

---

### Solution 1: FizzBuzz
```js
for (let i = 1; i <= 100; i++) {
  if (i % 15 === 0) console.log("FizzBuzz"); // Check "both" FIRST (15 = 3*5)
  else if (i % 3 === 0) console.log("Fizz");
  else if (i % 5 === 0) console.log("Buzz");
  else console.log(i);
}
```
**Key insight:** Checking `i % 15 === 0` first avoids needing to check both `%3` and `%5` separately for the "both" case.

### Solution 2: Reverse a String
```js
function reverseString(str) {
  let result = "";
  for (let i = str.length - 1; i >= 0; i--) {
    result += str[i];
  }
  return result;
}
// One-liner alternative: str.split("").reverse().join("")
```

### Solution 3: Palindrome Checker
```js
function isPalindrome(str) {
  const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, "");
  return cleaned === cleaned.split("").reverse().join("");
}
```

### Solution 4: Sum of Array
```js
function sumArray(arr) {
  let total = 0;
  for (const num of arr) total += num;
  return total;
}
// Modern equivalent: arr.reduce((sum, n) => sum + n, 0)
```

### Solution 5: Find the Largest Number
```js
function findMax(arr) {
  let max = arr[0];
  for (const num of arr) {
    if (num > max) max = num;
  }
  return max;
}
```

### Solution 6: Count Vowels
```js
function countVowels(str) {
  const vowels = "aeiouAEIOU";
  let count = 0;
  for (const char of str) {
    if (vowels.includes(char)) count++;
  }
  return count;
}
```

### Solution 7: Factorial
```js
// Loop-based
function factorialLoop(n) {
  let result = 1;
  for (let i = 2; i <= n; i++) result *= i;
  return result;
}

// Recursive (Module 05, chapter 05)
function factorialRecursive(n) {
  if (n <= 1) return 1; // Base case
  return n * factorialRecursive(n - 1); // Recursive case
}
```

### Solution 8: Temperature Converter
```js
function celsiusToFahrenheit(c) { return c * 9 / 5 + 32; }
function fahrenheitToCelsius(f) { return (f - 32) * 5 / 9; }
```

### Solution 9: Remove Duplicates
```js
function removeDuplicates(arr) {
  return [...new Set(arr)]; // Module 10, chapter 04 — Set automatically removes duplicates!
}
```

### Solution 10: Capitalize Each Word
```js
function titleCase(sentence) {
  return sentence
    .split(" ")
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join(" ");
}
```

---

**Back to problems:** [01-Beginner/01-Problems.md](../01-Beginner/01-Problems.md)
