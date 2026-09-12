# Syntax and Statements

**Module:** 01-JavaScript-Foundations
**Difficulty:** 🟢 Beginner
**Previous:** [03-How-JavaScript-Runs.md](./03-How-JavaScript-Runs.md)
**Next:** [05-Comments-and-Strict-Mode.md](./05-Comments-and-Strict-Mode.md)

---

## 1. Learning Objectives

- Statements aur expressions ka difference samajhna.
- JavaScript ke basic syntax rules seekhna (semicolons, whitespace, case-sensitivity).
- Identifiers aur naming rules jaanna.

## 2. Prerequisites

Modules 01-03.

## 3. Concept in Simple Hinglish

**Statement** ek complete instruction hai jo JavaScript ko "ye kaam karo" batati hai (jaise `let x = 5;`). **Expression** kuch bhi hai jo evaluate hokar ek **value** deta hai (jaise `5 + 3`, ya `x`). Har expression apne aap mein valid hai, lekin statement usually multiple expressions ko combine karke ek action banata hai.

## 4. Technical Explanation

JavaScript program **statements** ki sequence hoti hai, jo top se bottom execute hoti hai (control flow statements jaise loops/conditionals ke hisaab se order change ho sakta hai).

**Common statement types:**
- Declaration statements (`let`, `const`, `var`, `function`, `class`)
- Expression statements (`x = 5;`, `console.log(x);`)
- Control flow statements (`if`, `for`, `while`, `switch`)
- Jump statements (`return`, `break`, `continue`)

**Identifiers (naming rules):**
- Letters, digits, `_`, `$` allowed.
- Number se start nahi ho sakta.
- Case-sensitive hai — `myVar` aur `myvar` alag hain.
- Reserved keywords (`let`, `if`, `class`, etc.) naam ke liye use nahi ho sakte.

## 5. Syntax

```js
// Declaration statement
let x = 10;

// Expression statement
x + 5;          // valid expression, but value ka use nahi ho raha
console.log(x);

// Block statement
{
  let y = 20;
  console.log(y);
}
```

## 6. Basic Examples

```js
let a = 5;        // statement
let b = a + 10;    // "a + 10" ek expression hai, poori line statement hai
console.log(b);   // 15
```

## 7. Detailed Examples

```js
// Expression vs Statement clearly dikhane ke liye:

let result = (function () {
  return 42;
})(); // Yeh IIFE ek expression hai jo value return karti hai

console.log(result); // 42

// if-statement ek statement hai, expression nahi (value return nahi karta)
if (result === 42) {
  console.log("Match found");
}

// Lekin ternary operator ek EXPRESSION hai jo value deta hai
let message = result === 42 ? "Match found" : "No match";
console.log(message); // "Match found"
```

Yahan `if` statement kisi variable ko directly value assign nahi kar sakta, lekin ternary expression kar sakta hai — ye important distinction hai jab tum concise code likhna chahte ho.

## 8. Mental Model

Statement ko socho ek **complete sentence** ki tarah ("Mujhe paani chahiye."), aur expression ko socho ek **noun phrase** ki tarah jo apne aap mein value rakhta hai ("garam paani"). Sentence banane ke liye expressions ko combine karte hain, lekin ek phrase khud complete instruction nahi hoti.

## 9. What Happens Internally?

Parser code ko statements mein todhta hai, har statement ke andar expressions ko evaluate karta hai, aur result ko AST nodes mein represent karta hai. Engine phir in nodes ko sequence mein execute karta hai — top-to-bottom, jab tak control-flow statement (loop/condition) order change na kare.

## 10. Common Mistakes

- Variable names mein spaces ya hyphens use karna (`my-var` invalid hai — `-` minus operator samjha jaata hai).
- Reserved keywords ko variable naam banana (`let let = 5;` — error).
- Case-sensitivity bhool jaana — `Console.log()` (capital C) error dega, sahi hai `console.log()`.

## 11. Edge Cases

- `$` aur `_` valid identifier characters hain — libraries jaise jQuery isi liye `$` use karti hain (`$("div")`).
- Unicode characters bhi identifiers mein allowed hain kuch cases mein (jaise `let café = 5;`), lekin practical codebases mein English-only names recommended hain.

## 12. Real-World Usage

Clean, consistent naming conventions (camelCase for variables/functions, PascalCase for classes) professional codebases mein bahut important hoti hain — readability aur team collaboration ke liye.

## 13. Comparison With Related Concepts

| Concept | Returns a Value? | Example |
|---------|-------------------|---------|
| Statement | Usually No | `if (x) { ... }` |
| Expression | Yes | `x + 5`, `x > 10` |
| Expression Statement | The expression's value is discarded | `x + 5;` (alone on a line) |

## 14. Practice Questions

1. `let`, `if`, aur `x + 5` — inme se kaunsa statement hai aur kaunsa expression?
2. Ye variable naam valid hai ya nahi: `2ndValue`? Kyun?
3. `$price` ek valid variable name hai — sahi ya galat?

## 15. Challenge

5 different valid variable names likho jo camelCase follow karein, aur 3 invalid variable names likho jo common mistakes dikhaye (explain karo kyun invalid hain).

## 16. Interview Questions

**Q1: JavaScript mein statement aur expression mein kya difference hai?**
A: Expression evaluate hokar ek value produce karta hai (jaise `5 + 3`), jabki statement ek complete action perform karta hai aur necessarily value return nahi karta (jaise `if` statement). Har expression ek statement ban sakta hai ("expression statement"), lekin har statement expression nahi hota.

**Q2: JavaScript variable naming rules kya hain?**
A: Identifier letter, digit, `_`, ya `$` se contain ho sakta hai, lekin number se start nahi ho sakta. Reserved keywords use nahi ho sakte. JavaScript case-sensitive hai, matlab `value` aur `Value` alag identifiers hain.

## 17. Chapter Summary

Statements JavaScript program ki building blocks hain, jinke andar expressions evaluate hoti hain values produce karne ke liye. Naming rules (case-sensitivity, valid characters, reserved words) follow karna zaroori hai warna syntax errors aayenge.

## 18. Revision Checklist

- [ ] Statement vs expression clear hai with examples.
- [ ] Valid identifier rules yaad hain.
- [ ] Case-sensitivity ka concept clear hai.

---

**Next:** [05-Comments-and-Strict-Mode.md](./05-Comments-and-Strict-Mode.md)
