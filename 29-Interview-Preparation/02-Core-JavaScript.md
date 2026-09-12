# Interview Prep: Core JavaScript

**Module:** 29-Interview-Preparation
**Previous:** [01-Basic-Questions.md](./01-Basic-Questions.md)
**Next:** [03-Functions-and-Closures.md](./03-Functions-and-Closures.md)

---

**Q1: Hoisting kya hai?**
A: Hoisting JavaScript engine ka behavior hai jisme variable/function declarations ko unke scope ke top pe "conceptually move" kiya jaata hai execution se pehle (actually Creation Phase mein memory-allocation hoti hai). `var` `undefined` se initialize hoti hai, function declarations fully hoisted hoti hain, `let`/`const` TDZ mein rehte hain. (Detail: [02-Variables-and-Data-Types/03-Hoisting-and-TDZ.md](../02-Variables-and-Data-Types/03-Hoisting-and-TDZ.md))

**Q2: Temporal Dead Zone (TDZ) kya hai?**
A: TDZ wo period hai scope-start se lekar `let`/`const` variable ki actual declaration-line tak — is period mein variable ko access karna `ReferenceError` deta hai, chahe wo technically hoisted ho chuka ho. (Detail: same as above)

**Q3: Type coercion kya hai? Implicit aur explicit mein difference?**
A: Type coercion ek type ko dusre mein convert karna hai. Explicit coercion developer manually karta hai (`Number("5")`). Implicit coercion JavaScript automatically karta hai operators ke through (`"5" + 3` = `"53"`). (Detail: [09-Type-Coercion/02-Implicit-vs-Explicit-Coercion.md](../09-Type-Coercion/02-Implicit-vs-Explicit-Coercion.md))

**Q4: Falsy values ki complete list batao.**
A: `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN` — sirf ye 8. Baaki sab (including `[]` aur `{}`) truthy hain. (Detail: [09-Type-Coercion/01-Truthy-and-Falsy.md](../09-Type-Coercion/01-Truthy-and-Falsy.md))

**Q5: Scope chain kya hai?**
A: Scope chain wo path hai jise engine follow karta hai variable dhundne ke liye — current scope se shuru karke, har outer scope tak, jab tak variable mile ya global scope tak pahunch jaaye (phir `ReferenceError`). (Detail: [08-Scope-and-Closures/02-Lexical-Scope-and-Scope-Chain.md](../08-Scope-and-Closures/02-Lexical-Scope-and-Scope-Chain.md))

**Q6: Spread aur Rest operators mein kya difference hai?**
A: Dono `...` use karte hain, lekin opposite direction mein — Spread ek array/object ko expand karta hai individual elements mein. Rest multiple values ko ek array mein collect karta hai. (Detail: [03-Operators/05-Spread-and-Rest.md](../03-Operators/05-Spread-and-Rest.md))

**Q7: `||` aur `??` mein kya difference hai?**
A: `||` sabhi falsy values (0, "", false, null, undefined, NaN) pe fallback deta hai. `??` sirf `null`/`undefined` pe fallback deta hai — 0 ya "" jaisi valid falsy values ko preserve karta hai. (Detail: [03-Operators/03-Logical-Nullish-Optional-Chaining.md](../03-Operators/03-Logical-Nullish-Optional-Chaining.md))

**Q8: Optional chaining (`?.`) kya karta hai?**
A: Nested property/method access ko safely handle karta hai — agar koi beech ki property `null`/`undefined` hai, poori expression `undefined` return karti hai bina error throw kiye. (Detail: same as above)

**Q9: Array copying shallow hoti hai ya deep by default?**
A: Shallow — spread (`[...arr]`), `slice()`, `Array.from()` sab sirf top-level ko copy karte hain, nested objects/arrays still reference-shared rehte hain. Deep copy ke liye `structuredClone()` chahiye. (Detail: [06-Arrays/04-Iteration-and-Advanced-Methods.md](../06-Arrays/04-Iteration-and-Advanced-Methods.md))

**Q10: `map()`, `filter()`, `reduce()` mein difference batao.**
A: `map()` har element transform karke same-length array return karta hai. `filter()` matching elements select karke subset array return karta hai. `reduce()` poore array ko ek single value mein combine karta hai (sum, object, etc.). (Detail: [06-Arrays/03-Non-Mutating-Methods.md](../06-Arrays/03-Non-Mutating-Methods.md))

**Q11: Object destructuring mein default values kaise kaam karti hain?**
A: `{ key = defaultValue }` — sirf tab default apply hoti hai jab `key` ki value `undefined` ho (missing ya explicitly undefined), `null` ya koi aur value pe default trigger nahi hoti. (Detail: [07-Objects/02-Destructuring-and-Spread.md](../07-Objects/02-Destructuring-and-Spread.md))

**Q12: `Object.freeze()` deep immutability deta hai?**
A: Nahi — sirf shallow. Top-level properties lock ho jaati hain, lekin nested objects/arrays ke andar ki properties abhi bhi mutable rehti hain. (Detail: [07-Objects/03-Object-Methods.md](../07-Objects/03-Object-Methods.md))

---

**Next:** [03-Functions-and-Closures.md](./03-Functions-and-Closures.md)
