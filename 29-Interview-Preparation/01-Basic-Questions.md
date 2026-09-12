# Interview Prep: Basic Questions

**Module:** 29-Interview-Preparation
**Previous:** [28-Coding-Problems/Solutions/07-Real-World-Problems-Solutions.md](../28-Coding-Problems/Solutions/07-Real-World-Problems-Solutions.md)
**Next:** [02-Core-JavaScript.md](./02-Core-JavaScript.md)

---

**Q1: JavaScript kya hai?**
A: JavaScript ek dynamically-typed, multi-paradigm, interpreted/JIT-compiled programming language hai, originally web-pages ko interactive banane ke liye bani thi. Aaj ye browsers ke saath-saath Node.js ke through servers, CLI-tools, aur mobile-apps (React Native) mein bhi use hoti hai. Isse ECMAScript specification standardize karti hai. (Full detail: [01-JavaScript-Foundations/01-What-is-JavaScript.md](../01-JavaScript-Foundations/01-What-is-JavaScript.md))

**Q2: `var`, `let`, `const` mein kya difference hai?**
A: `var` function-scoped hai, hoist hoke `undefined` ban jaata hai, redeclare ho sakta hai. `let` block-scoped hai, TDZ mein rehta hai declaration tak, reassign ho sakta hai. `const` bhi block-scoped hai, TDZ mein rehta hai, lekin reassign nahi ho sakta (though object/array contents mutate ho sakte hain). Modern code default `const` use karta hai, zaroorat pe `let`. (Detail: [02-Variables-and-Data-Types/02-var-let-const.md](../02-Variables-and-Data-Types/02-var-let-const.md))

**Q3: JavaScript ke primitive data types kaunse hain?**
A: 7 primitive types: `string`, `number`, `bigint`, `boolean`, `undefined`, `null`, `symbol`. Ye immutable hote hain aur "copy by value" behavior follow karte hain. (Detail: [02-Variables-and-Data-Types/04-Primitive-Types.md](../02-Variables-and-Data-Types/04-Primitive-Types.md))

**Q4: `null` aur `undefined` mein kya difference hai?**
A: `undefined` matlab variable declared hai lekin value assign nahi hui (JavaScript automatically deta hai). `null` ek explicit value hai jo developer khud "intentionally no value" batane ke liye assign karta hai. `typeof undefined` "undefined" hai, `typeof null` "object" hai (historic bug). (Detail: [02-Variables-and-Data-Types/04-Primitive-Types.md](../02-Variables-and-Data-Types/04-Primitive-Types.md))

**Q5: `==` aur `===` mein kya difference hai?**
A: `==` (loose equality) comparison se pehle type-coercion karta hai. `===` (strict equality) koi coercion nahi karta, type aur value dono match hone chahiye. Best practice hamesha `===` use karna hai. (Detail: [03-Operators/02-Comparison-and-Equality.md](../03-Operators/02-Comparison-and-Equality.md))

**Q6: JavaScript mein arrays kaise declare karte hain?**
A: `let arr = [1, 2, 3];` (array literal, recommended) ya `let arr = new Array(1, 2, 3);` (constructor, less common). Arrays technically objects hain JavaScript mein, zero-indexed hote hain. (Detail: [06-Arrays/01-Array-Basics.md](../06-Arrays/01-Array-Basics.md))

**Q7: Function declaration aur function expression mein kya difference hai?**
A: Function declaration (`function name() {}`) fully hoisted hoti hai, declaration se pehle call ho sakti hai. Function expression (`const name = function() {}`) variable-hoisting rules follow karti hai, definition se pehle call nahi ho sakti. (Detail: [05-Functions/01-Function-Basics.md](../05-Functions/01-Function-Basics.md))

**Q8: Template literals kya hain?**
A: Backticks (`` ` ``) use karke banaye gaye strings jo `${expression}` syntax se variable-interpolation aur multi-line strings support karte hain — string-concatenation (`+`) ka modern, readable alternative.

**Q9: JavaScript mein comments kaise likhte hain?**
A: Single-line: `// comment`. Multi-line: `/* comment */`. (Detail: [01-JavaScript-Foundations/05-Comments-and-Strict-Mode.md](../01-JavaScript-Foundations/05-Comments-and-Strict-Mode.md))

**Q10: `NaN` kya hai?**
A: "Not-a-Number" — ek special numeric value jo invalid/unrepresentable math-operations ka result represent karta hai (jaise `0/0`). Interestingly, `typeof NaN` "number" hai, aur `NaN === NaN` `false` hai — reliable check ke liye `Number.isNaN()` use karo. (Detail: [02-Variables-and-Data-Types/04-Primitive-Types.md](../02-Variables-and-Data-Types/04-Primitive-Types.md))

---

**Next:** [02-Core-JavaScript.md](./02-Core-JavaScript.md)
