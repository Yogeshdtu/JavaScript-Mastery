# Interview Prep: Objects and Prototypes

**Module:** 29-Interview-Preparation
**Previous:** [03-Functions-and-Closures.md](./03-Functions-and-Closures.md)
**Next:** [05-Async-JavaScript.md](./05-Async-JavaScript.md)

---

**Q1: Prototype chain kya hai?**
A: Har object ka internal `[[Prototype]]` link hota hai kisi dusre object ki taraf. Property na milne pe, engine is chain ko traverse karta hai (object → prototype → prototype ka prototype → ...) jab tak property mile ya chain `null` pe end ho jaaye. (Detail: [10-Modern-JavaScript/03-Prototypes-Deep-Dive.md](../10-Modern-JavaScript/03-Prototypes-Deep-Dive.md))

**Q2: JavaScript classes "real" classes hain (jaise Java mein)?**
A: Nahi — classes syntactic sugar hain prototype-based inheritance ke upar. `class Dog extends Animal` internally `Dog.prototype` ko `Animal.prototype` se link karta hai, koi separate "class" mechanism engine mein nahi hai. (Detail: [10-Modern-JavaScript/02-Classes-and-Inheritance.md](../10-Modern-JavaScript/02-Classes-and-Inheritance.md), [10-Modern-JavaScript/03-Prototypes-Deep-Dive.md](../10-Modern-JavaScript/03-Prototypes-Deep-Dive.md))

**Q3: `super()` ka use kya hai?**
A: Child class constructor mein parent-constructor ko call karta hai (`this` use karne se pehle zaroori hai). `super.method()` parent ke specific method ko call karta hai jab child ne usse override kiya ho. (Detail: [10-Modern-JavaScript/02-Classes-and-Inheritance.md](../10-Modern-JavaScript/02-Classes-and-Inheritance.md))

**Q4: `Object.create(null)` se banaya object special kyun hai?**
A: Iska `[[Prototype]]` `null` hota hai — koi inherited methods (`toString`, `hasOwnProperty`) available nahi hote, ek "bare" object hai. (Detail: [10-Modern-JavaScript/03-Prototypes-Deep-Dive.md](../10-Modern-JavaScript/03-Prototypes-Deep-Dive.md))

**Q5: `hasOwnProperty()` aur `in` operator mein kya difference hai?**
A: `in` object ki apni **aur inherited** properties dono check karta hai. `hasOwnProperty()` sirf **own** properties check karta hai, inherited ko exclude karta hai. (Detail: [03-Operators/06-typeof-instanceof-in-delete.md](../03-Operators/06-typeof-instanceof-in-delete.md))

**Q6: Getter/setter kya hote hain?**
A: Special methods jo property-access syntax ki tarah behave karte hain but function execute karte hain — getters computed-values ke liye, setters validation-logic ke liye useful hain. (Detail: [07-Objects/04-Property-Descriptors.md](../07-Objects/04-Property-Descriptors.md))

**Q7: Private class fields (`#field`) `_field` naming-convention se kaise different hain?**
A: `#field` engine-enforced **true privacy** hai — bahar se access karne ki koshish `SyntaxError` deti hai. `_field` sirf ek convention hai, actual enforcement nahi karta, bahar se accessible hai. (Detail: [10-Modern-JavaScript/07-Private-Fields-and-Other-Features.md](../10-Modern-JavaScript/07-Private-Fields-and-Other-Features.md))

**Q8: `Map` aur plain Object mein kya difference hai?**
A: `Map` kisi bhi type ki key support karta hai (objects bhi), insertion-order guaranteed hai, `.size` directly milta hai. Objects sirf string/symbol keys support karte hain. (Detail: [10-Modern-JavaScript/04-Map-Set-WeakMap-WeakSet.md](../10-Modern-JavaScript/04-Map-Set-WeakMap-WeakSet.md))

**Q9: Symbols ka purpose kya hai?**
A: Hamesha unique values banate hain — object property-keys mein naming-collisions avoid karne ke liye use hote hain. Normal enumeration (`Object.keys`, `for...in`) se hidden rehte hain. (Detail: [10-Modern-JavaScript/06-Symbols.md](../10-Modern-JavaScript/06-Symbols.md))

**Q10: `instanceof` internally kaise kaam karta hai?**
A: Ye check karta hai ki constructor ka `.prototype` object dusre object ki prototype-chain mein kahi milta hai ya nahi. Customizable bhi hai `Symbol.hasInstance` ke through. (Detail: [10-Modern-JavaScript/03-Prototypes-Deep-Dive.md](../10-Modern-JavaScript/03-Prototypes-Deep-Dive.md), [24-Advanced-JavaScript-Internals/03-Iterators-Generators-Symbols-Deep-Dive.md](../24-Advanced-JavaScript-Internals/03-Iterators-Generators-Symbols-Deep-Dive.md))

---

**Next:** [05-Async-JavaScript.md](./05-Async-JavaScript.md)
