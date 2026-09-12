# Recursion

**Module:** 05-Functions
**Difficulty:** 🟡 Intermediate
**Previous:** [04-Callbacks-and-Higher-Order-Functions.md](./04-Callbacks-and-Higher-Order-Functions.md)
**Next:** [06-Closures.md](./06-Closures.md)

---

## 1. Learning Objectives

- Recursion ka concept samajhna — function khud ko call kaise karta hai.
- Base case aur recursive case likhna.
- Stack overflow ka risk samajhna.

## 2. Prerequisites

Module 05, chapters 01-04.

## 3. Concept in Simple Hinglish

Recursion ek technique hai jisme **function khud ko call karta hai** ek chhoti version ke problem ko solve karne ke liye, jab tak ek "base case" (stopping point) na aa jaaye. Ye loops ka alternative hai kuch specific problems ke liye — especially jab problem naturally "smaller sub-problems" mein todha ja sake (jaise tree structures, factorial, Fibonacci).

## 4. Technical Explanation

Har recursive function mein 2 essential parts hote hain:

1. **Base Case:** Wo condition jaha recursion **rukta hai** — bina isके, function infinite baar call hota rahega (jaise infinite loop).
2. **Recursive Case:** Wo part jaha function **khud ko call karta hai** ek chhote/simpler input ke saath.

## 5. Syntax

```js
function recursiveFunction(input) {
  if (baseCaseCondition) {
    return baseValue; // Base case
  }
  return recursiveFunction(smallerInput); // Recursive case
}
```

## 6. Basic Examples

```js
function factorial(n) {
  if (n <= 1) return 1;       // Base case
  return n * factorial(n - 1); // Recursive case
}

console.log(factorial(5)); // 120 (5*4*3*2*1)
```

## 7. Detailed Examples

**Step-by-step execution trace — factorial(4):**
```
factorial(4)
= 4 * factorial(3)
= 4 * (3 * factorial(2))
= 4 * (3 * (2 * factorial(1)))
= 4 * (3 * (2 * 1))          <- base case reached, factorial(1) = 1
= 4 * (3 * 2)
= 4 * 6
= 24
```
Har call apne aap ko **call stack** pe rakhti hai (Module 08 mein detail), aur jab base case milta hai, calls **reverse order mein resolve** hoti hain — isse "unwinding the stack" kehte hain.

**Fibonacci sequence — classic recursion example:**
```js
function fibonacci(n) {
  if (n <= 1) return n; // Base case
  return fibonacci(n - 1) + fibonacci(n - 2); // Two recursive calls
}

console.log(fibonacci(6)); // 8 (sequence: 0,1,1,2,3,5,8)
```

**Recursion for nested/tree structures — a real-world use case:**
```js
function sumNestedArray(arr) {
  let total = 0;
  for (const item of arr) {
    if (Array.isArray(item)) {
      total += sumNestedArray(item); // Recursive call for nested arrays
    } else {
      total += item;
    }
  }
  return total;
}

console.log(sumNestedArray([1, [2, 3], [4, [5, 6]]])); // 21
```
Ye example dikhata hai recursion **naturally nested structures** (jaise deeply nested arrays, folder structures, DOM trees) ke liye perfect fit hai — loops se ye kaafi complex hota.

**⚠️ Missing base case — stack overflow:**
```js
function infiniteRecursion(n) {
  return infiniteRecursion(n + 1); // No base case!
}
// infiniteRecursion(1); // ❌ RangeError: Maximum call stack size exceeded
```

## 8. Mental Model

Recursion ko socho **Russian nesting dolls (matryoshka)** ki tarah — har doll ke andar ek chhoti doll hai, jab tak sabse chhoti doll (base case) na mile. Phir tum wapas bahar aate ho, ek-ek doll band karte hue (results combine karte hue).

## 9. What Happens Internally?

Har recursive call ek **naya stack frame** call stack pe add karta hai (apna memory, apne local variables ke saath). Jab base case hit hota hai, frames **pop** hone lagte hain reverse order mein, aur results combine hote hain. Bahut deep recursion (bina proper base case, ya bahut large input) call stack ki limit cross kar sakti hai — "Stack Overflow" error.

## 10. Common Mistakes

- Base case likhna bhool jaana — infinite recursion → stack overflow.
- Base case ko galat condition dena, jisse wo kabhi trigger na ho.
- Recursion ka overuse karna simple problems ke liye jaha simple loop zyada efficient/readable hota.

## 11. Edge Cases

- Naive recursive Fibonacci (upar wala example) **bahut inefficient** hai large `n` ke liye — same sub-problems repeatedly calculate hote hain (exponential time complexity). Isko fix karne ke liye "memoization" (Module 22 - Performance) use hota hai.
- JavaScript engines mein "Tail Call Optimization" (TCO) technically ES6 spec mein hai, lekin **most engines (V8 included) isse implement nahi karte** — isliye deep recursion practically risky rehti hai JavaScript mein, unlike kuch dusri languages.

## 12. Real-World Usage

Recursion tree/graph traversal (DOM manipulation, file system navigation, JSON deeply nested parsing), divide-and-conquer algorithms (quicksort, mergesort), aur backtracking problems (maze solving) mein use hota hai.

## 13. Comparison With Related Concepts

| Aspect | Loop | Recursion |
|--------|------|-----------|
| Best for | Simple repetition | Nested/tree-like structures |
| Memory usage | Constant | Grows with recursion depth (call stack) |
| Readability for hierarchical data | Complex | Often much simpler |
| Risk | Infinite loop | Stack overflow |

## 14. Practice Questions

1. Recursion ke 2 essential parts kya hain?
2. Base case missing hone se kya error aata hai?
3. Recursion kis type ke problems ke liye naturally suited hai?

## 15. Challenge

Ek recursive function `sumRange(n)` likho jo 1 se `n` tak sabhi numbers ka sum return kare (bina loop use kiye). Phir isi function ka iterative (loop-based) version likh ke compare karo.

## 16. Interview Questions

**Q1: Recursion kya hai aur ek recursive function ke essential parts kya hote hain?**
A: Recursion ek technique hai jisme function khud ko call karta hai problem ko chhoti sub-problems mein todhne ke liye. Har recursive function mein do parts hone chahiye: **base case** (jo recursion ko rokta hai) aur **recursive case** (jo function ko chhote input ke saath dobara call karta hai). Base case ke bina, function infinite baar call hota rahega jab tak stack overflow na ho jaaye.

**Q2: Recursion aur loops mein kab kaunsa use karna better hai?**
A: Loops generally simple, linear repetition ke liye better hain — kam memory use karte hain (constant space) aur zyada performant hote hain simple cases mein. Recursion naturally hierarchical/nested problems (tree traversal, nested data structures, divide-and-conquer algorithms) ke liye zyada readable aur intuitive hota hai, lekin har call stack frame consume karta hai, isliye bahut deep recursion "stack overflow" risk laata hai — especially JavaScript mein jaha tail-call optimization widely implemented nahi hai.

## 17. Chapter Summary

Recursion function ko khud ko call karne deta hai chhote sub-problems solve karne ke liye, jab tak base case na mile. Ye tree-like/nested structures ke liye natural fit hai, lekin missing base case ya bahut deep recursion "stack overflow" ka risk create karti hai.

## 18. Revision Checklist

- [ ] Base case aur recursive case dono identify kar sakta hoon.
- [ ] Recursion trace (step-by-step) karna aata hai.
- [ ] Stack overflow kab hota hai, samajh gaya.

---

**Next:** [06-Closures.md](./06-Closures.md)
