# Interview Prep: Functions and Closures

**Module:** 29-Interview-Preparation
**Previous:** [02-Core-JavaScript.md](./02-Core-JavaScript.md)
**Next:** [04-Objects-and-Prototypes.md](./04-Objects-and-Prototypes.md)

---

**Q1: Closure kya hai? Example ke saath.**
A: Closure tab banta hai jab ek function apne outer (lexical) scope ki variables ko "yaad" rakhta hai, chahe outer function ka execution complete ho gaya ho. Example: `function makeCounter() { let count = 0; return () => ++count; }` — returned function `count` ko access karta rehta hai. (Detail: [05-Functions/06-Closures.md](../05-Functions/06-Closures.md))

**Q2: Closures ka practical use-case batao.**
A: Data privacy (module pattern — private variables jo bahar se access nahi ho sakte), function factories (jaise `multiply(2)` se `double` function banana), aur stateful callbacks (debounce/throttle, memoization). (Detail: [08-Scope-and-Closures/04-Closures-Deep-Dive.md](../08-Scope-and-Closures/04-Closures-Deep-Dive.md))

**Q3: Loop mein `var` ke saath closures galat value kyun capture karte hain?**
A: `var` function-scoped hai, saare loop-iterations same shared variable use karte hain. Closures baad mein call hone pe uski **final** value dekhte hain. `let` block-scoped hai, har iteration ka apna independent binding hota hai. (Detail: [05-Functions/06-Closures.md](../05-Functions/06-Closures.md))

**Q4: Arrow functions aur regular functions mein `this` ka behavior kaise different hai?**
A: Regular functions ka `this` call-time pe dynamically decide hota hai (call-site pe depend karta hai). Arrow functions apna `this` binding nahi rakhte — lexically (jaha define hue) surrounding scope se `this` inherit karte hain. (Detail: [05-Functions/02-Arrow-Functions.md](../05-Functions/02-Arrow-Functions.md), [08-Scope-and-Closures/05-this-Keyword.md](../08-Scope-and-Closures/05-this-Keyword.md))

**Q5: `call()`, `apply()`, `bind()` mein kya difference hai?**
A: `call`/`apply` function ko turant call karte hain explicit `this` ke saath (difference: arguments individually vs array). `bind()` naya function return karta hai jiska `this` permanently fixed hai, baad mein call karne ke liye. (Detail: [08-Scope-and-Closures/06-call-apply-bind.md](../08-Scope-and-Closures/06-call-apply-bind.md))

**Q6: Higher-order function kya hai?**
A: Function jo dusre function ko argument ki tarah accept kare, ya function return kare (ya dono). Example: `array.map(callback)`. (Detail: [05-Functions/04-Callbacks-and-Higher-Order-Functions.md](../05-Functions/04-Callbacks-and-Higher-Order-Functions.md))

**Q7: Pure function kya hai?**
A: Function jo (1) same inputs ke liye hamesha same output de, aur (2) koi side-effects na produce kare (external state modify na kare). Predictable, testable. (Detail: [05-Functions/07-IIFE-Composition-Pure-Functions.md](../05-Functions/07-IIFE-Composition-Pure-Functions.md))

**Q8: Default parameters kab trigger hoti hain?**
A: Sirf tab jab corresponding argument `undefined` ho (missing ya explicitly `undefined` diya gaya) — `null` pass karne se default trigger nahi hoti. (Detail: [05-Functions/03-Parameters-Arguments-Defaults-Rest.md](../05-Functions/03-Parameters-Arguments-Defaults-Rest.md))

**Q9: Recursion mein "stack overflow" kab hota hai?**
A: Jab base case missing ho ya recursion bahut deep chali jaaye (bina proper termination ke) — har recursive call ek naya call-stack frame add karta hai, engine ki maximum stack-size cross hone pe `RangeError` aata hai. (Detail: [05-Functions/05-Recursion.md](../05-Functions/05-Recursion.md))

**Q10: IIFE (Immediately Invoked Function Expression) kya hai?**
A: Function jo define hote hi turant call ho jaata hai — `(function(){...})()`. Historically scope-isolation ke liye use hota tha, aaj ES Modules ne is need ko largely replace kar diya hai. (Detail: [05-Functions/07-IIFE-Composition-Pure-Functions.md](../05-Functions/07-IIFE-Composition-Pure-Functions.md))

**Q11: `arguments` object aur rest parameters mein kya difference hai?**
A: `arguments` array-like object hai (real array nahi), sirf regular functions mein available. Rest parameters (`...args`) real array hain, arrow functions mein bhi kaam karte hain. Modern code rest parameters prefer karta hai. (Detail: [05-Functions/03-Parameters-Arguments-Defaults-Rest.md](../05-Functions/03-Parameters-Arguments-Defaults-Rest.md))

---

**Next:** [04-Objects-and-Prototypes.md](./04-Objects-and-Prototypes.md)
