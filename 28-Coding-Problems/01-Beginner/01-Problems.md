# Beginner Coding Problems

**Module:** 28-Coding-Problems
**Previous:** [00-How-To-Use-This-Library.md](../00-How-To-Use-This-Library.md)
**Solutions:** [Solutions/01-Beginner-Solutions.md](../Solutions/01-Beginner-Solutions.md)

---

### Problem 1: FizzBuzz
**Difficulty:** 🟢
**Concepts Tested:** Loops, conditionals, modulus (Module 04, 03)
**Problem:** 1 se 100 tak numbers print karo. Multiples of 3 ke liye "Fizz", multiples of 5 ke liye "Buzz", dono ke liye "FizzBuzz".
**Expected Approach:** `for` loop + modulus operator (`%`) + `if-else` chain, checking the "both" case first.

### Problem 2: Reverse a String
**Difficulty:** 🟢
**Concepts Tested:** Strings, arrays (Module 06)
**Problem:** Ek function `reverseString(str)` likho jo string ko reverse karke return kare, bina built-in `.reverse()` ke.
**Expected Approach:** Loop backward through the string, building a new string; or convert to array manually via a loop.

### Problem 3: Palindrome Checker
**Difficulty:** 🟢
**Concepts Tested:** Strings, conditionals
**Problem:** Ek function `isPalindrome(str)` likho jo check kare string palindrome hai ya nahi (case-insensitive, ignore spaces).
**Expected Approach:** Clean the string (lowercase, remove non-alphanumeric), compare it to its reverse.

### Problem 4: Sum of Array
**Difficulty:** 🟢
**Concepts Tested:** Arrays, loops (Module 06)
**Problem:** Ek function `sumArray(arr)` likho jo array ke sabhi numbers ka sum return kare — pehle loop se, phir `reduce()` se.
**Expected Approach:** Accumulator pattern — start at 0, add each element.

### Problem 5: Find the Largest Number
**Difficulty:** 🟢
**Concepts Tested:** Arrays, comparison operators
**Problem:** Ek function `findMax(arr)` likho jo array ka largest number return kare, bina `Math.max()` ke.
**Expected Approach:** Track a running "max" variable, compare each element against it.

### Problem 6: Count Vowels
**Difficulty:** 🟢
**Concepts Tested:** Strings, loops
**Problem:** Ek function `countVowels(str)` likho jo string mein vowels (a,e,i,o,u) ki count return kare.
**Expected Approach:** Loop through characters, check membership in a vowel-set/string.

### Problem 7: Factorial
**Difficulty:** 🟢
**Concepts Tested:** Loops OR Recursion (Module 05, chapter 05)
**Problem:** Ek function `factorial(n)` likho — dono tareeke se (loop-based aur recursive), aur compare karo.
**Expected Approach:** Loop: multiply from 1 to n. Recursion: base case `n <= 1`, recursive case `n * factorial(n-1)`.

### Problem 8: Temperature Converter
**Difficulty:** 🟢
**Concepts Tested:** Functions, arithmetic operators (Module 05, 03)
**Problem:** Do functions likho: `celsiusToFahrenheit(c)` aur `fahrenheitToCelsius(f)`.
**Expected Approach:** Apply the standard formulas: `F = C * 9/5 + 32`, `C = (F - 32) * 5/9`.

### Problem 9: Remove Duplicates from Array
**Difficulty:** 🟢
**Concepts Tested:** Arrays, Set (Module 06, 10)
**Problem:** Ek function `removeDuplicates(arr)` likho jo unique values ka array return kare.
**Expected Approach:** Use `Set` for the cleanest solution, or manually track "seen" values with `includes()`.

### Problem 10: Capitalize Each Word
**Difficulty:** 🟢
**Concepts Tested:** Strings, array methods
**Problem:** Ek function `titleCase(sentence)` likho jo har word ka first letter capitalize kare.
**Expected Approach:** Split by spaces, capitalize each word's first character, rejoin.

---

**Next:** [02-Intermediate/01-Problems.md](../02-Intermediate/01-Problems.md) | **Solutions:** [Solutions/01-Beginner-Solutions.md](../Solutions/01-Beginner-Solutions.md)
