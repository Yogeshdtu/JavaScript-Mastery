# Interview Prep: Coding Questions

**Module:** 29-Interview-Preparation
**Previous:** [08-Output-Based-Questions.md](./08-Output-Based-Questions.md)
**Next:** [10-Advanced-Questions.md](./10-Advanced-Questions.md)

---

These are classic live-coding interview questions. Try each before checking the approach. Full solved versions of similar problems: [28-Coding-Problems/](../28-Coding-Problems/).

**Q1: Write a function to check if two strings are anagrams of each other.**
```js
function isAnagram(str1, str2) {
  const normalize = (s) => s.toLowerCase().split("").sort().join("");
  return normalize(str1) === normalize(str2);
}
```
**Talking points:** Mention time-complexity (`O(n log n)` due to sort) and that a frequency-map approach could achieve `O(n)` if asked to optimize.

**Q2: Write a function to find the second-largest number in an array.**
```js
function secondLargest(arr) {
  let first = -Infinity, second = -Infinity;
  for (const num of arr) {
    if (num > first) { second = first; first = num; }
    else if (num > second && num !== first) { second = num; }
  }
  return second;
}
```
**Talking points:** Single-pass `O(n)` solution — better than sorting (`O(n log n)`) then picking `arr[arr.length-2]`.

**Q3: Implement a function to check for balanced parentheses (with multiple bracket-types).**
See full solution: [28-Coding-Problems/Solutions/06-Interview-Level-Solutions.md](../28-Coding-Problems/Solutions/06-Interview-Level-Solutions.md#solution-2-valid-parentheses) — practice explaining the stack-based approach out loud.

**Q4: Write a function that returns the frequency count of characters in a string.**
```js
function charFrequency(str) {
  return [...str].reduce((freq, char) => { // Module 06, chapter 03's reduce; Module 06, chapter 04's spread on strings
    freq[char] = (freq[char] || 0) + 1;
    return freq;
  }, {});
}
```

**Q5: Implement `Array.prototype.flat()` from scratch (any depth).**
```js
function myFlat(arr, depth = 1) {
  if (depth === 0) return arr.slice();
  return arr.reduce((flat, item) => {
    return flat.concat(Array.isArray(item) ? myFlat(item, depth - 1) : item);
  }, []);
}
```

**Q6: Write a function to find the first non-repeating character in a string.**
```js
function firstNonRepeating(str) {
  const counts = {};
  for (const char of str) counts[char] = (counts[char] || 0) + 1;
  for (const char of str) if (counts[char] === 1) return char;
  return null;
}
```
**Talking points:** Two passes but still `O(n)` overall — explain why a hash-map beats nested-loop `O(n²)` checking.

**Q7: Implement a `chunk(array, size)` function that splits an array into smaller arrays of a given size.**
```js
function chunk(array, size) {
  const result = [];
  for (let i = 0; i < array.length; i += size) {
    result.push(array.slice(i, i + size)); // Module 06, chapter 03
  }
  return result;
}
console.log(chunk([1,2,3,4,5], 2)); // [[1,2],[3,4],[5]]
```

**Q8: Write a function to check if a number is prime.**
```js
function isPrime(n) {
  if (n < 2) return false;
  for (let i = 2; i <= Math.sqrt(n); i++) { // Only need to check up to sqrt(n)
    if (n % i === 0) return false;
  }
  return true;
}
```
**Talking points:** Explain WHY checking only up to `Math.sqrt(n)` is sufficient (a factor pair always has one member ≤ √n).

**Q9: Implement a simple `pipe` function that composes multiple functions left-to-right.**
```js
function pipe(...fns) {
  return (initialValue) => fns.reduce((value, fn) => fn(value), initialValue); // Module 06, ch 03
}
const addOne = x => x + 1;
const double = x => x * 2;
const pipeline = pipe(addOne, double);
console.log(pipeline(3)); // (3+1)*2 = 8
```

**Q10: Write a function to check if a string has all unique characters (without extra data structures).**
```js
function hasUniqueChars(str) {
  for (let i = 0; i < str.length; i++) {
    for (let j = i + 1; j < str.length; j++) {
      if (str[i] === str[j]) return false;
    }
  }
  return true;
}
```
**Talking points:** This is `O(n²)` intentionally (the "without extra data structures" constraint) — be ready to explain the `O(n)` alternative using a `Set` if the interviewer relaxes the constraint.

---

**Next:** [10-Advanced-Questions.md](./10-Advanced-Questions.md)
