# Mutating Array Methods

**Module:** 06-Arrays
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [01-Array-Basics.md](./01-Array-Basics.md)
**Next:** [03-Non-Mutating-Methods.md](./03-Non-Mutating-Methods.md)

---

## 1. Learning Objectives

- Array methods jo **original array ko modify** karte hain, samajhna aur use karna: `push`, `pop`, `shift`, `unshift`, `splice`, `sort`, `reverse`.
- "Mutating" ka matlab clearly samajhna.

## 2. Prerequisites

[01-Array-Basics.md](./01-Array-Basics.md)

## 3. Concept in Simple Hinglish

**Mutating methods** wo array methods hain jo **original array ko directly change** kar dete hain — naya array nahi banate. Ye important hai samajhna kyunki agar tumne kahi aur bhi usi array ka reference rakha hai, wo bhi change dekhega (Module 02, chapter 05 ka "reference types" concept yaad karo).

## 4. Technical Explanation

| Method | Action | Returns |
|--------|--------|---------|
| `push(...items)` | End mein add karta hai | New length |
| `pop()` | End se remove karta hai | Removed element |
| `shift()` | Start se remove karta hai | Removed element |
| `unshift(...items)` | Start mein add karta hai | New length |
| `splice(start, count, ...items)` | Kahi bhi add/remove/replace | Removed elements (array) |
| `sort(compareFn)` | Order badalta hai | The same array (sorted) |
| `reverse()` | Order reverse karta hai | The same array (reversed) |

## 5. Syntax

```js
arr.push(item);
arr.pop();
arr.shift();
arr.unshift(item);
arr.splice(start, deleteCount, ...itemsToAdd);
arr.sort(compareFunction);
arr.reverse();
```

## 6. Basic Examples

```js
let arr = [1, 2, 3];
arr.push(4);      // arr = [1, 2, 3, 4]
arr.pop();          // arr = [1, 2, 3], returns 4
arr.unshift(0);    // arr = [0, 1, 2, 3]
arr.shift();        // arr = [1, 2, 3], returns 0
```

## 7. Detailed Examples

**`splice()` — the most powerful (and confusing) mutating method:**
```js
let items = ["a", "b", "c", "d", "e"];

// Remove 2 elements starting at index 1
let removed = items.splice(1, 2);
console.log(items);   // ["a", "d", "e"]
console.log(removed); // ["b", "c"]

// Insert without removing (deleteCount = 0)
items.splice(1, 0, "X", "Y");
console.log(items); // ["a", "X", "Y", "d", "e"]

// Replace elements
items.splice(0, 1, "REPLACED");
console.log(items); // ["REPLACED", "X", "Y", "d", "e"]
```

**`sort()` — the default behavior trap:**
```js
let numbers = [10, 1, 21, 2];
numbers.sort();
console.log(numbers); // [1, 10, 2, 21] ⚠️ WRONG for numbers!
```
`sort()` by **default** elements ko **strings** ki tarah compare karta hai — isi liye `10` `"2"` se pehle aata hai (lexicographic: "1" < "2"). Numbers ko correctly sort karne ke liye **compare function** dena zaroori hai:
```js
numbers.sort((a, b) => a - b); // Ascending
console.log(numbers); // [1, 2, 10, 21] ✅

numbers.sort((a, b) => b - a); // Descending
console.log(numbers); // [21, 10, 2, 1]
```

**Compare function ka logic:**
- Agar `a - b` **negative** hai, `a` `b` se pehle aata hai.
- Agar `a - b` **positive** hai, `b` `a` se pehle aata hai.
- Agar `0` hai, order unchanged rehta hai.

**Mutating methods affect all references (reference-type behavior):**
```js
let original = [1, 2, 3];
let alias = original; // Same reference, NOT a copy

alias.push(4);
console.log(original); // [1, 2, 3, 4] — original also changed!
```

## 8. Mental Model

Mutating methods ko socho **original document ko directly edit karna** (Google Docs mein "edit mode") — koi bhi jo document dekh raha hai wo change turant dekhega. Non-mutating methods (next chapter) ek **"copy banake usme edit karna"** jaisa hai — original safe rehta hai.

## 9. What Happens Internally?

Ye methods array ke internal memory representation ko **in-place modify** karte hain — array ka reference same rehta hai, sirf uski internal contents/length change hoti hai. Isi liye variable ko reassign karne ki zaroorat nahi hoti (`arr = arr.push(x)` galat pattern hai — `push` number return karta hai, array nahi).

## 10. Common Mistakes

- `sort()` ko numbers pe bina compare function ke use karna.
- `push()`/`pop()` ke return value ko galat samajhna — `push` naya length return karta hai, array nahi.
- Mutating methods use karna jab immutability chahiye ho (jaise React state) — us case mein non-mutating alternatives (next chapter) use karo.

## 11. Edge Cases

```js
console.log([].pop());  // undefined (empty array, nothing to remove)
console.log([].shift()); // undefined

let arr = [3, 1, 4, 1, 5];
arr.sort(); // Default sort, converts to strings for comparison
console.log(arr); // [1, 1, 3, 4, 5] — works here by coincidence, single digits
```

## 12. Real-World Usage

`push`/`pop` stack-like operations mein common (jaise undo history, browser back-navigation stack). `splice` list-editing UI (todo list item remove/insert) mein use hota hai. `sort` leaderboards, sorted lists, search results ranking mein.

## 13. Comparison With Related Concepts

| Method | Mutates Original? | Non-Mutating Alternative |
|--------|---------------------|-----------------------------|
| `push`/`pop` | Yes | `[...arr, item]` / `arr.slice(0, -1)` |
| `sort` | Yes | `[...arr].sort()` (spread first, then sort the copy) |
| `splice` | Yes | `slice()` + spread (next chapter) |

## 14. Practice Questions

1. `[10, 1, 21, 2].sort()` bina compare function ke kya result dega?
2. `splice(2, 0, "x")` ka effect kya hoga array pe?
3. Mutating method use karne ka main risk kya hai?

## 15. Challenge

Ek array `tasks = ["Task A", "Task B", "Task C"]` diya hai. `splice()` use karke: (1) "Task B" ko "Task B - Updated" se replace karo, (2) ek naya "Task D" end se pehle insert karo.

## 16. Interview Questions

**Q1: `sort()` method numbers ko galat order mein kyun sort karta hai bina compare function ke?**
A: `sort()` by default elements ko **strings** mein convert karke unicode/lexicographic order mein compare karta hai. Isi liye `[10, 2]` mein "10" "2" se pehle aata hai kyunki string comparison mein "1" < "2" hai character-by-character. Numbers ko sahi (numeric) order mein sort karne ke liye ek compare function pass karna zaroori hai: `arr.sort((a, b) => a - b)`.

**Q2: `push()` aur `splice()` mutating methods hain — iska practical implication kya hai?**
A: Mutating methods **original array ko directly modify** karte hain, naya array nahi banate. Iska matlab hai agar array ka reference kisi aur variable ya function mein bhi hold kiya gaya hai, wo change bhi wahi dekhega. Ye libraries jaise React mein problematic hai jaha state ko immutably update karna zaroori hota hai — isi liye wahan non-mutating alternatives (spread, `map`, `filter`) prefer kiye jaate hain.

## 17. Chapter Summary

`push`, `pop`, `shift`, `unshift`, `splice`, `sort`, `reverse` — sab original array ko in-place modify karte hain (mutating). `sort()` default string comparison use karta hai, numbers ke liye explicit compare function chahiye. Mutating methods reference-shared arrays mein sab jagah change reflect karte hain.

## 18. Revision Checklist

- [ ] Mutating methods ki list aur unka behavior yaad hai.
- [ ] `sort()` ka compare-function trick clear hai.
- [ ] `splice()` ka add/remove/replace syntax samajh gaya.

---

**Next:** [03-Non-Mutating-Methods.md](./03-Non-Mutating-Methods.md)
