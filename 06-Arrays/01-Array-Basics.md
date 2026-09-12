# Array Basics

**Module:** 06-Arrays
**Difficulty:** 🟢 Beginner
**Previous:** [05-Functions/07-IIFE-Composition-Pure-Functions.md](../05-Functions/07-IIFE-Composition-Pure-Functions.md)
**Next:** [02-Mutating-Methods.md](./02-Mutating-Methods.md)

---

## 1. Learning Objectives

- Array create karna aur elements access/modify karna.
- Array ki `length` property aur indexing samajhna.
- Multi-dimensional arrays ka basic idea paana.

## 2. Prerequisites

Module 05 complete.

## 3. Concept in Simple Hinglish

Array ek **ordered list** hai jisme multiple values ek saath store ki ja sakti hain — jaise ek shelf jisme books numbered slots mein rakhi hain (0 se start hoke). JavaScript arrays flexible hain — same array mein different types ki values (number, string, object) rakh sakte ho.

## 4. Technical Explanation

Arrays JavaScript mein technically **objects** hain (special behavior ke saath) — indices numeric string keys hote hain internally, aur `length` automatically maintain hoti hai. Indexing **zero-based** hai — pehla element index `0` pe hai.

## 5. Syntax

```js
let arr = [element1, element2, element3];
let empty = [];
let mixed = [1, "two", true, { four: 4 }];

arr[0];       // Access first element
arr[0] = x;   // Modify first element
arr.length;   // Number of elements
```

## 6. Basic Examples

```js
let fruits = ["apple", "banana", "cherry"];
console.log(fruits[0]);      // "apple"
console.log(fruits.length);  // 3

fruits[1] = "blueberry";
console.log(fruits); // ["apple", "blueberry", "cherry"]
```

## 7. Detailed Examples

**Accessing out-of-range indices:**
```js
let colors = ["red", "green"];
console.log(colors[5]); // undefined — no error, just undefined
```

**Modifying length directly — a lesser-known trick:**
```js
let nums = [1, 2, 3, 4, 5];
nums.length = 3;
console.log(nums); // [1, 2, 3] — truncates the array!

nums.length = 5;
console.log(nums); // [1, 2, 3, <2 empty items>] — extends with empty slots
```

**Multi-dimensional arrays (arrays of arrays):**
```js
let grid = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];

console.log(grid[1][2]); // 6 (row index 1, column index 2)

for (let row = 0; row < grid.length; row++) {
  for (let col = 0; col < grid[row].length; col++) {
    console.log(grid[row][col]);
  }
}
```

**Sparse arrays — a subtle edge case:**
```js
let sparse = [1, , 3]; // Middle element missing
console.log(sparse.length); // 3
console.log(sparse[1]);      // undefined
console.log(1 in sparse);    // false — the slot doesn't even exist, unlike a normal undefined value
```

## 8. Mental Model

Array ko socho ek **numbered locker row** ki tarah — locker 0, locker 1, locker 2... Har locker mein kuch bhi rakh sakte ho. `length` batata hai total kitne lockers "officially" hain (highest index + 1).

## 9. What Happens Internally?

JavaScript engines (V8 included) arrays ko performance ke liye specially optimize karte hain jab wo "dense" (consecutive indices, same-ish types) hote hain — internally contiguous memory jaisi structure use ho sakti hai. Lekin sparse arrays ya mixed-type arrays banate hi, engine slower, generic object-like storage pe switch kar deta hai — isi liye consistent, dense arrays performance ke liye better hoti hain.

## 10. Common Mistakes

- Array ki `length` ko manually chhota set karke socha ki sirf display change hoga — actually elements permanently delete ho jaate hain.
- Sparse arrays banana accidentally (trailing commas, `delete` operator use karna) — Module 03, chapter 06 mein `delete` ka array-related issue dekha tha.
- Array ko object ki tarah string keys ke saath use karna (`arr["name"] = "x"`) — technically possible hai but arrays ka purpose numeric-indexed data ke liye hai; named data ke liye object use karo.

## 11. Edge Cases

```js
let arr = [1, 2, 3];
arr["3"] = 4; // String "3" works same as numeric 3 for arrays
console.log(arr); // [1, 2, 3, 4]
console.log(arr.length); // 4
```

## 12. Real-World Usage

Arrays har jagah use hoti hain — API se aayi list of items, form ke multiple inputs, table rows, todo-list items, shopping cart products — koi bhi "collection of similar things" array mein store hoti hai.

## 13. Comparison With Related Concepts

| Aspect | Array | Object |
|--------|-------|--------|
| Order | Guaranteed (index-based) | Not guaranteed (though modern engines mostly preserve insertion order) |
| Access | By numeric index | By named key |
| Best for | Lists of similar items | Named/structured data |

## 14. Practice Questions

1. `let arr = [10, 20, 30]; arr.length = 1;` — array ka final content kya hoga?
2. Multi-dimensional array se ek specific element access karne ka syntax likho.
3. Sparse array kya hota hai?

## 15. Challenge

Ek 3x3 grid (2D array) banao jisme tic-tac-toe board represent ho (`"X"`, `"O"`, `""` values ke saath). Ek nested loop likh ke poora grid console mein print karo formatted way mein.

## 16. Interview Questions

**Q1: JavaScript mein arrays technically kya hote hain?**
A: JavaScript arrays technically **objects** hain, special behavior ke saath — indices numeric-string keys ki tarah kaam karte hain, aur `length` property automatically maintain hoti hai based on highest index. Isi liye `typeof []` `"object"` return karta hai, `"array"` nahi — array-specific checking ke liye `Array.isArray()` use karna padta hai.

**Q2: Array ki `length` property ko directly modify karne se kya hota hai?**
A: `length` ko chhota set karne se array **truncate** ho jaata hai — extra elements permanently remove ho jaate hain. `length` ko bada set karne se array "empty slots" (sparse array) ke saath extend ho jaata hai. Ye ek less-known but powerful/dangerous feature hai — accidentally length modify karna data-loss bug create kar sakta hai.

## 17. Chapter Summary

Arrays ordered, zero-indexed collections hain jo JavaScript mein technically objects hain. `length` property automatically maintain hoti hai aur directly modify bhi ki ja sakti hai (truncate/extend). Multi-dimensional arrays (arrays of arrays) grid-like data represent karne ke liye use hoti hain.

## 18. Revision Checklist

- [ ] Zero-based indexing clear hai.
- [ ] `length` property ka read aur direct-modify behavior samajh gaya.
- [ ] Multi-dimensional array access syntax yaad hai.

---

**Next:** [02-Mutating-Methods.md](./02-Mutating-Methods.md)
