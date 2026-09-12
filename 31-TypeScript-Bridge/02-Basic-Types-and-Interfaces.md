# Basic Types and Interfaces

**Module:** 31-TypeScript-Bridge (Bonus Module)
**Difficulty:** 🟡 Intermediate
**Previous:** [01-Why-TypeScript-and-Setup.md](./01-Why-TypeScript-and-Setup.md)
**Next:** [03-Functions-Generics-and-Utility-Types.md](./03-Functions-Generics-and-Utility-Types.md)

---

## 1. Learning Objectives

- Primitive aur complex types (array, object, union, literal) annotate karna.
- `interface` aur `type` se custom shapes define karna.
- `any`, `unknown`, aur `never` ke beech ka farak samajhna.

## 2. Prerequisites

[01-Why-TypeScript-and-Setup.md](./01-Why-TypeScript-and-Setup.md)

## 3. Concept in Simple Hinglish

Module 01 mein humne dekha ki TypeScript variables ko types deta hai. Ab hum dekhenge ki **objects ki poori shape** (kaunse properties honi chahiye, kaunse optional hain, kaunse type ke) kaise describe karte hain — jaise ek blueprint jo batata hai "ek `User` object mein `name` (string), `age` (number), aur optionally `email` (string) hona chahiye." Isके liye TypeScript `interface` deta hai.

## 4. Technical Explanation

**Basic type annotations:**
```ts
let name: string = "Yogesh";
let age: number = 25;
let isAdmin: boolean = false;
let tags: string[] = ["js", "ts"];          // array of strings
let coords: [number, number] = [10, 20];    // tuple — fixed length aur types
let id: string | number = "abc123";          // union type — dono mein se koi ek
let status: "pending" | "done" | "failed" = "pending"; // literal union — sirf ye 3 values allowed
```

**`interface`** — object ki shape define karta hai:
```ts
interface User {
  name: string;
  age: number;
  email?: string;    // ? = optional property
  readonly id: number; // readonly = ek baar set hone ke baad change nahi ho sakta
}

const user: User = { name: "Yogesh", age: 25, id: 1 }; // email optional isliye theek hai
```

**`type`** — similar purpose, thoda zyada flexible (unions/intersections ke liye better):
```ts
type ID = string | number;
type Point = { x: number; y: number };
```

## 5. Syntax

```ts
interface Name {
  property: Type;
  optionalProperty?: Type;
  readonly immutableProperty: Type;
}

type AliasName = Type1 | Type2;
```

## 6. Basic Examples

```ts
interface Product {
  name: string;
  price: number;
  inStock: boolean;
}

function displayProduct(product: Product): string {
  return `${product.name}: ₹${product.price}`;
}
```

## 7. Detailed Examples

**`any` vs `unknown` — dono "kuch bhi ho sakta hai" jaise lagte hain, lekin bahut different hain:**
```ts
let riskyValue: any = getSomeData();
riskyValue.someMethod();     // ✅ TypeScript koi complaint nahi karega — even agar method exist na kare!

let safeValue: unknown = getSomeData();
safeValue.someMethod();      // ❌ Error — pehle type verify karna padega
if (typeof safeValue === "string") {
  safeValue.toUpperCase();   // ✅ Ab safe hai, type narrow ho gaya
}
```
`any` TypeScript ke saare type-checks **band** kar deta hai us variable ke liye — isse "TypeScript ka escape hatch" kehte hain, aur jitna kam use karo utna better. `unknown` type-safe alternative hai — value use karne se pehle type verify karwata hai.

**Interface extension (inheritance jaisa):**
```ts
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const myDog: Dog = { name: "Tommy", breed: "Labrador" };
```

**Union types se real-world API response handle karna:**
```ts
interface SuccessResponse {
  status: "success";
  data: string[];
}

interface ErrorResponse {
  status: "error";
  message: string;
}

type ApiResponse = SuccessResponse | ErrorResponse;

function handleResponse(response: ApiResponse) {
  if (response.status === "success") {
    console.log(response.data);       // TypeScript jaanta hai ye SuccessResponse hai
  } else {
    console.log(response.message);    // Yaha ErrorResponse hai
  }
}
```
Ye pattern — **discriminated unions** — real-world TypeScript codebases mein extremely common hai, especially API responses aur state management ke liye.

## 8. Mental Model

`interface` ko socho ek **form template** ki tarah — jaise kisi government form mein fields defined hote hain (Name: ___, Age: ___, Email (optional): ___). Koi bhi object jo `User` interface follow karta hai, us "form" ko sahi tarike se fill karna zaroori hai — required fields miss nahi ho sakte, wrong-type values nahi bhari ja sakti.

## 9. What Happens Internally?

TypeScript **structural typing** use karta hai — matlab ye check nahi karta "iska naam `User` interface hai ya nahi", balki check karta hai "iski shape `User` jaisi hai ya nahi" (duck typing: "agar duck jaisa dikhta hai aur quack karta hai, to duck hai"). Isliye do completely unrelated interfaces bhi compatible ho sakti hain agar unki shape same ho:
```ts
interface Point2D { x: number; y: number; }
interface Coordinate { x: number; y: number; }

const p: Point2D = { x: 1, y: 2 };
const c: Coordinate = p; // ✅ Works — same shape, naam matter nahi karta
```

## 10. Common Mistakes

- `any` ko overuse karna "TypeScript errors fix karne" ke liye — isse TypeScript ka poora purpose hi khatam ho jaata hai. Jab type pata na ho, `unknown` use karo.
- Optional property (`?`) aur `undefined`-allowing union (`string | undefined`) mein confuse hona — dono similar hain lekin `?` property ko completely omit karne deta hai object se.
- `interface` aur `type` ko lekar over-thinking karna — dono mein se koi bhi use karo consistently; objects ke liye `interface` slightly more conventional hai, unions ke liye `type` zaroori hai.

## 11. Edge Cases

- Do `interface` declarations same naam ke saath automatically merge ho jaati hain ("declaration merging") — ye `type` ke saath possible nahi hai (duplicate `type` naam error deta hai).
- `readonly` sirf **compile-time** protection hai — runtime pe (compiled JS mein) property still normally mutable rehti hai, TypeScript sirf tumhe accidentally modify karne se rokta hai code likhte waqt.

## 12. Real-World Usage

- API response shapes define karna — frontend ko exactly pata hota hai backend se kya aayega.
- Component props (React mein `interface Props { title: string; onClick: () => void; }`).
- Configuration objects — function ko complex config pass karte waqt, IDE autocomplete se sab options dikh jaate hain.

## 13. Comparison With Related Concepts

| Feature | `interface` | `type` |
|---------|-------------|--------|
| Object shapes | ✅ Best fit | ✅ Works too |
| Union/intersection types | ❌ Not directly | ✅ Designed for this |
| Declaration merging | ✅ Yes | ❌ No (error on duplicate) |
| Extending | `extends` keyword | `&` intersection |

## 14. Practice Questions

1. `any` aur `unknown` mein practical farak kya hai?
2. Optional property (`?`) kaise define karte hain interface mein?
3. Discriminated union kya hota hai aur kab useful hai?

## 15. Challenge

Ek `interface Task { id: number; title: string; completed: boolean; dueDate?: string; }` define karo, phir ek function `formatTask(task: Task): string` likho jo task ki details ek readable string mein return kare, `dueDate` present hone par usko bhi include kare.

## 16. Interview Questions

**Q1: TypeScript "structural typing" kya hai, aur ye Java/C# ke "nominal typing" se kaise alag hai?**
A: Structural typing mein compatibility object ki **shape** (properties aur unke types) ke basis pe decide hoti hai, naam pe nahi — agar do types ki shape same hai, wo interchangeable hain, chahe unke naam alag ho. Nominal typing (Java/C# mein) mein compatibility explicit declaration pe depend karti hai (`implements`/`extends` likhna zaroori hai), sirf same shape hona kaafi nahi hai.

**Q2: `any` type use karna kab justified hai, aur kab nahi?**
A: `any` sirf tab justified hai jab kisi third-party JavaScript library ke saath integrate kar rahe ho jiske type definitions available nahi hain, aur temporarily type-checking bypass karna zaroori ho — aur ideally us jagah `// TODO` comment ke saath jald type improve karne ka plan ho. Regular application code mein `any` avoid karna chahiye — `unknown` (safe) ya proper interface define karna better practice hai, kyunki `any` puri type-safety ko silently disable kar deta hai.

## 17. Chapter Summary

TypeScript primitive types, arrays, tuples, aur union/literal types support karta hai. `interface` object shapes define karta hai (optional `?`, `readonly` modifiers ke saath); `type` zyada flexible hai unions ke liye. TypeScript structural typing use karta hai — shape match karta hai to compatible hai, naam irrelevant hai. `any` type-safety poori tarah disable karta hai; `unknown` safe alternative hai jo usage se pehle verification maangta hai.

## 18. Revision Checklist

- [ ] Basic type annotations (string, number, array, union, literal) likh sakta hoon.
- [ ] `interface` se object shape define kar sakta hoon, optional/readonly ke saath.
- [ ] `any` vs `unknown` ka farak clear hai.
- [ ] Discriminated unions ka basic idea samajh gaya.

---

**Next:** [03-Functions-Generics-and-Utility-Types.md](./03-Functions-Generics-and-Utility-Types.md)
