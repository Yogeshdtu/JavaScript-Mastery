# var, let, const

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟢 Beginner
**Previous:** [01-Variables-Basics.md](./01-Variables-Basics.md)
**Next:** [03-Hoisting-and-TDZ.md](./03-Hoisting-and-TDZ.md)

---

## 1. Learning Objectives

- `var`, `let`, aur `const` ke beech ka difference deeply samajhna.
- Kab kaunsa use karna chahiye, ye decide kar paana.
- Scope aur redeclaration rules jaanna.

## 2. Prerequisites

[01-Variables-Basics.md](./01-Variables-Basics.md)

## 3. Concept in Simple Hinglish

JavaScript mein variable banane ke 3 tareeke hain — `var` (purana, ⚠️ legacy), `let` (✅ modern, jab value change ho sakti hai), aur `const` (✅ modern, jab value fix rehni hai). Aaj ke time mein professional code **almost hamesha `let` aur `const`** use karta hai — `var` sirf purane codebases mein milta hai.

## 4. Technical Explanation

| Feature | `var` ⚠️ Legacy | `let` ✅ Modern | `const` ✅ Modern |
|---------|----------------|-----------------|---------------------|
| Scope | Function-scoped | Block-scoped | Block-scoped |
| Redeclaration | Allowed | Not allowed (same scope) | Not allowed |
| Reassignment | Allowed | Allowed | **Not allowed** |
| Hoisting | Hoisted with `undefined` | Hoisted, but in TDZ | Hoisted, but in TDZ |
| Global object property | Yes (in browsers) | No | No |

**Block-scoped** matlab `{ }` ke andar variable sirf usi block mein accessible hai. **Function-scoped** matlab variable pura function ke andar accessible hai, chahe kisi bhi block mein declare hua ho.

## 5. Syntax

```js
var oldStyle = "legacy";
let changeable = "can change";
const fixed = "cannot reassign";
```

## 6. Basic Examples

```js
let count = 1;
count = 2;      // ✅ valid — let reassign ho sakta hai
console.log(count); // 2

const PI = 3.14;
// PI = 3.14159; // ❌ TypeError: Assignment to constant variable.
```

## 7. Detailed Examples

**Scope difference — `var` vs `let`:**
```js
function testVar() {
  if (true) {
    var x = "I leaked out of the block!";
  }
  console.log(x); // "I leaked out of the block!" — var block-scoped nahi hai
}
testVar();

function testLet() {
  if (true) {
    let y = "I stay inside the block";
  }
  console.log(y); // ReferenceError: y is not defined
}
testLet();
```

`var` sirf function-scoped hoti hai, isliye `if` block ke andar declare hone ke baad bhi wo bahar accessible rahi. `let` block-scoped hai, isliye block ke bahar accessible nahi hai — yahi behavior zyada predictable aur bugs se bachata hai.

**`const` ke saath objects/arrays — ek common confusion:**
```js
const user = { name: "Aman" };
user.name = "Rohit"; // ✅ Valid! Object ki property change ho rahi hai
console.log(user);   // { name: "Rohit" }

// user = { name: "New Object" }; // ❌ TypeError — reference change nahi ho sakta
```

`const` sirf **variable ke reference/binding** ko lock karta hai, object/array ke **andar ki content** ko nahi. Isi ko "const objects are not immutable" kehte hain.

## 8. Mental Model

`var` ko socho ek aisi cheez jo **pure ghar mein kahi bhi ghoom sakti hai** (function scope) — control karna difficult hai. `let`/`const` ko socho ek cheez jo **sirf apne kamre (block) tak limited** hai — predictable aur safe.

`const` ek "sealed label" hai jo box pe chipka hai — box (reference) badal nahi sakta, lekin box ke andar ki cheezein (object properties) rearrange ho sakti hain.

## 9. What Happens Internally?

- `var` declarations function ke top pe "hoist" hoti hain aur turant `undefined` se initialize ho jaati hain.
- `let`/`const` bhi hoist hoti hain, lekin ek special zone mein rehti hain jise **Temporal Dead Zone (TDZ)** kehte hain — jab tak actual declaration line execute nahi hoti, unhe access karna error dega (detail next chapter mein).

## 10. Common Mistakes

- `var` use karna aur loops mein unexpected scope leakage ka bug face karna.
- `const` ko "immutable" samajhna — galat! `const` object/array ki content change ho sakti hai.
- Har jagah `let` use karna jab `const` better fit hai — best practice hai default `const` use karo, sirf reassignment zaroori ho tab `let`.

## 11. Edge Cases

- Same scope mein `let x` ko dobara `let x` se declare karna **SyntaxError** dega — `var` mein ye allowed hai (silently overwrite hota hai).
- `const` array mein `.push()`, `.pop()` jaise mutating methods chal sakte hain kyunki wo reference nahi badalte, sirf content modify karte hain.

## 12. Real-World Usage

Modern style guides (Airbnb, Google) recommend karte hain: **hamesha `const` use karo by default, sirf jab reassignment zaroori ho tab `let` use karo. `var` kabhi mat use karo** naye code mein — sirf legacy code padhte time samajhna zaroori hai.

## 13. Comparison With Related Concepts

**✅ Modern / Recommended:** `let` aur `const`
**⚠️ Legacy / Common in Existing Codebases:** `var` — purane codebases, tutorials, aur legacy libraries mein milega, isliye samajhna zaroori hai lekin naya code isse avoid kare.

## 14. Practice Questions

1. `var`, `let`, `const` mein se kaunsa reassignment allow nahi karta?
2. `const arr = [1,2,3]; arr.push(4);` — kya ye valid hai? Kyun?
3. `var` function-scoped hai ya block-scoped?

## 15. Challenge

Ek code snippet likho jisme `var` ka scope-leakage bug demonstrate ho (loop ke andar `var` use karke), phir usi bug ko `let` se fix karo.

## 16. Interview Questions

**Q1: `let` aur `var` mein main difference kya hai?**
A: Sabse bada difference **scope** ka hai — `var` function-scoped hoti hai, `let` block-scoped. Isi wajah se `if`/`for` block ke andar declare `var` block ke bahar bhi accessible rehti hai, jabki `let` sirf apne block tak limited rehti hai. Dusra difference: `var` ko dobara declare kar sakte ho same scope mein, `let` nahi. Teesra: `var` hoist hoke `undefined` ban jaati hai, `let` TDZ mein rehti hai jab tak declaration line na aaye.

**Q2: Kya `const` variable ki value change nahi ho sakti?**
A: `const` variable ka **binding/reference** reassign nahi ho sakta. Lekin agar value ek object ya array hai, to uski internal properties/elements modify ho sakte hain (`const obj = {}; obj.key = "value";` valid hai). Agar full immutability chahiye to `Object.freeze()` use karna padta hai.

## 17. Chapter Summary

`var` (⚠️ legacy) function-scoped hai aur predictable nahi hai — modern code isse avoid karta hai. `let` (✅) reassignable, block-scoped variable ke liye hai. `const` (✅) fixed reference ke liye hai — but objects/arrays ke andar mutation allowed hai. Default `const` use karo, zaroorat pe `let`.

## 18. Revision Checklist

- [ ] `var` vs `let` scope difference clear hai.
- [ ] `const` ka "reference lock, not deep immutability" concept clear hai.
- [ ] Kab `let` aur kab `const` use karna hai, decide kar sakta hoon.

---

**Next:** [03-Hoisting-and-TDZ.md](./03-Hoisting-and-TDZ.md)
