# Execution Context and Call Stack

**Module:** 08-Scope-and-Closures
**Difficulty:** 🟠 Advanced
**Previous:** [02-Lexical-Scope-and-Scope-Chain.md](./02-Lexical-Scope-and-Scope-Chain.md)
**Next:** [04-Closures-Deep-Dive.md](./04-Closures-Deep-Dive.md)

---

## 1. Learning Objectives

- Execution Context kya hota hai aur uske phases (Creation, Execution) samajhna.
- Call Stack ka mechanism samajhna.
- Stack overflow ka exact cause samajhna.

## 2. Prerequisites

Module 08, chapters 01-02.

## 3. Concept in Simple Hinglish

**Execution Context** ek "environment/container" hai jo JavaScript engine banata hai jab bhi koi code chalta hai — isme us code ki variables, functions, aur `this` value store hoti hai. **Call Stack** un execution contexts ka "stack" (pile) hai — jaise plates ka stack, jo function call hota hai wo upar add hota hai, jo return hota hai wo upar se remove hota hai.

## 4. Technical Explanation

**Types of Execution Context:**
1. **Global Execution Context (GEC):** Sabse pehle banta hai, program start hote hi. Ek hi hota hai poore program mein.
2. **Function Execution Context (FEC):** Har function call pe naya banta hai.
3. **Eval Execution Context:** `eval()` code ke liye (rare, avoid karna chahiye).

**Har Execution Context ke 2 phases hote hain:**
1. **Creation Phase:** Variable Environment banta hai (hoisting hoti hai — `var`→`undefined`, `let/const`→TDZ, function declarations fully hoisted), `this` binding decide hoti hai, scope chain set hoti hai.
2. **Execution Phase:** Code line-by-line actually run hota hai, values assign hoti hain.

**Call Stack:** LIFO (Last In, First Out) structure jo track karta hai "abhi kaunsa function execute ho raha hai" aur "kisne kisko call kiya".

## 5. Syntax

Execution context/call stack ka koi direct syntax nahi hai — ye conceptual/internal mechanism hai jo har function call pe automatically hota hai.

## 6. Basic Examples

```js
function first() {
  console.log("Inside first");
}

function second() {
  first();
  console.log("Inside second");
}

second();
// Call Stack progression:
// 1. [Global]
// 2. [Global, second]
// 3. [Global, second, first]
// 4. [Global, second] (first returns, popped off)
// 5. [Global] (second returns, popped off)
```

## 7. Detailed Examples

**Tracing the call stack step-by-step:**
```js
function multiply(a, b) {
  return a * b;
}

function square(n) {
  return multiply(n, n);
}

function printSquare(n) {
  console.log(square(n));
}

printSquare(5);
```

**Call stack trace:**
```
Step 1: printSquare(5) called → Stack: [Global, printSquare]
Step 2: square(5) called      → Stack: [Global, printSquare, square]
Step 3: multiply(5, 5) called → Stack: [Global, printSquare, square, multiply]
Step 4: multiply returns 25   → Stack: [Global, printSquare, square]
Step 5: square returns 25     → Stack: [Global, printSquare]
Step 6: console.log(25) runs  → Stack: [Global, printSquare]
Step 7: printSquare returns   → Stack: [Global]
```

**Stack overflow — when the stack gets too deep:**
```js
function recurseForever() {
  return recurseForever(); // No base case!
}

// recurseForever(); // ❌ RangeError: Maximum call stack size exceeded
```
Har call ek naya frame call stack pe add karta hai. Bina base case ke, stack **overflow** ho jaata hai — browser/Node.js ek maximum stack size enforce karte hain (typically kuch thousand frames, exact number engine-dependent).

**Reading a real stack trace — a practical debugging skill:**
```js
function levelC() {
  throw new Error("Something broke!");
}
function levelB() {
  levelC();
}
function levelA() {
  levelB();
}
// levelA();
// Error: Something broke!
//     at levelC (file.js:2)
//     at levelB (file.js:5)
//     at levelA (file.js:8)
//     at Object.<anonymous> (file.js:10)
```
Stack trace **top se bottom** padha jaata hai — sabse upar wo function hai jaha actual error hua (`levelC`), phir uske caller (`levelB`), phir uska caller (`levelA`) — ye "call chain" reverse order mein dikhati hai.

## 8. Mental Model

Call Stack ko socho **plates ka stack** — har function call ek naya plate upar rakhta hai. Jab function return karta hai, uska plate hata diya jaata hai (top se). Execution Context ko socho har function ka apna **temporary office** jaha uska apna desk (variables), apna staff-directory (scope chain), aur apna ID card (`this`) hota hai — office band hone (function return) ke baad staff-directory usually khatam ho jaati hai, jab tak koi closure use "yaad" na rakhe.

## 9. What Happens Internally?

Engine (V8) call stack ko ek fixed-size memory region mein maintain karta hai. Har function call ek naya "stack frame" push karta hai jisme local variables, parameters, aur "return address" (kaha wapas jaana hai) store hota hai. Jab function return hota hai, frame pop ho jaata hai aur execution wahi se continue hota hai jaha caller ne chhoda tha.

## 10. Common Mistakes

- Deep recursion likhna bina base case ke — stack overflow.
- "Execution context" aur "scope" ko exactly same samajhna — execution context ek broader concept hai jisme scope (Variable Environment), `this`, aur execution phase sab shamil hain.
- Stack trace ko bottom-to-top padhna jab actually top-to-bottom (error location se caller chain tak) padhna chahiye.

## 11. Edge Cases

Async code (Module 13-15) call stack ko directly involve nahi karta jab wo "wait" kar raha hota hai — `setTimeout` callback stack se completely bahar chala jaata hai jab tak wo actually execute hone ke liye ready na ho (event loop ke through). Isliye async debugging thoda different feel hota hai — stack traces mein "async" boundaries dikhti hain.

## 12. Real-World Usage

Stack traces production error-logging tools (Sentry, LogRocket) mein critical hain bugs debug karne ke liye. Deep recursion avoid karna (ya iterative approach use karna) large-data-processing applications mein stack overflow se bachne ke liye important hai.

## 13. Comparison With Related Concepts

| Concept | Relation |
|---------|----------|
| Scope Chain | Part of an Execution Context's Variable Environment |
| Closures | Rely on Execution Context surviving via references |
| `this` | Determined during an Execution Context's Creation Phase |

## 14. Practice Questions

1. Execution Context ke 2 phases kya hain?
2. Call Stack LIFO kyun hai?
3. Stack overflow kis situation mein hota hai?

## 15. Challenge

3 functions likho jo ek dusre ko call karein (A calls B calls C), aur manually likh ke dikhao ki call stack kaise build aur unwind hoti hai step by step (jaise upar wala trace example).

## 16. Interview Questions

**Q1: Execution Context kya hai aur uske phases kya hain?**
A: Execution Context ek abstract "container/environment" hai jo JavaScript engine har code-execution (global code ya function call) ke liye banata hai — isme variables, function declarations, aur `this` binding store hoti hai. Iske 2 phases hain: (1) **Creation Phase** — hoisting hoti hai (`var` → `undefined`, function declarations fully hoisted, `let`/`const` TDZ mein), `this` decide hota hai, scope chain set hoti hai; (2) **Execution Phase** — code line-by-line actually run hota hai, real values assign hoti hain.

**Q2: Call Stack kya hai aur "stack overflow" kab hota hai?**
A: Call Stack ek LIFO (Last-In-First-Out) data structure hai jo track karti hai kaunsa function currently execute ho raha hai aur kisne kisko call kiya. Jab function call hota hai, ek naya "frame" stack pe push hota hai; jab function return hota hai, frame pop ho jaata hai. Stack Overflow tab hota hai jab bahut zyada nested function calls (usually infinite/deep recursion bina proper base case ke) stack ki maximum allowed size cross kar jaati hain — engine `RangeError: Maximum call stack size exceeded` throw kar deta hai.

## 17. Chapter Summary

Execution Context har code-execution ke liye ek environment banata hai (Creation + Execution phases). Call Stack function calls ko LIFO order mein track karta hai. Deep/infinite recursion bina base case ke stack overflow cause karti hai. Stack traces (top-to-bottom) debugging ke liye error ki poori call-chain dikhate hain.

## 18. Revision Checklist

- [ ] Execution Context ke Creation aur Execution phases yaad hain.
- [ ] Call Stack ka LIFO mechanism trace kar sakta hoon.
- [ ] Stack overflow ka cause aur real stack trace padhna aata hai.

---

**Next:** [04-Closures-Deep-Dive.md](./04-Closures-Deep-Dive.md)
