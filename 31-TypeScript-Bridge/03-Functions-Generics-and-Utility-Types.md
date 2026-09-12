# Functions, Generics, and Utility Types

**Module:** 31-TypeScript-Bridge (Bonus Module)
**Difficulty:** 🟠 Intermediate-Advanced
**Previous:** [02-Basic-Types-and-Interfaces.md](./02-Basic-Types-and-Interfaces.md)
**Next:** [04-Migrating-JS-to-TS-and-Real-World-Usage.md](./04-Migrating-JS-to-TS-and-Real-World-Usage.md)

---

## 1. Learning Objectives

- Functions ke parameters, return types, aur optional/default params ko type karna.
- **Generics** samajhna — reusable, type-safe code likhne ka tareeka.
- Common **utility types** (`Partial`, `Pick`, `Omit`, `Record`) use karna.

## 2. Prerequisites

[02-Basic-Types-and-Interfaces.md](./02-Basic-Types-and-Interfaces.md), Module 05 (Functions) ka comfortable knowledge.

## 3. Concept in Simple Hinglish

Ab tak humne fixed types dekhe hain (`string`, `number`, specific interfaces). Lekin kabhi-kabhi tumhe ek **generic/reusable function** chahiye hoti hai jo kisi bhi type ke saath kaam kare, lekin phir bhi type-safe rahe — jaise ek function jo array ka pehla element return kare, chahe wo numbers ka array ho ya strings ka. Isके liye TypeScript **generics** deta hai — ek "type variable" jo call-time pe decide hota hai.

## 4. Technical Explanation

**Function typing:**
```ts
function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}

function processUser(user: { name: string; age?: number }): void {
  console.log(user.name);
}

// Arrow function typing
const add = (a: number, b: number): number => a + b;
```

**Generics** — `<T>` ek placeholder type hai:
```ts
function getFirstElement<T>(arr: T[]): T {
  return arr[0];
}

getFirstElement<number>([1, 2, 3]);        // T = number, return type: number
getFirstElement<string>(["a", "b"]);        // T = string, return type: string
getFirstElement([true, false]);             // T inferred as boolean — explicit <T> zaroori nahi
```

`T` ka koi special meaning nahi hai — ye sirf convention hai (jaise `i` loop counters ke liye). Isse "type parameter" bhi kehte hain.

## 5. Syntax

```ts
function name<T>(param: T): T { }
interface Box<T> { value: T; }
type Wrapper<T> = { data: T };
```

## 6. Basic Examples

```ts
function identity<T>(value: T): T {
  return value;
}

console.log(identity<string>("hello"));  // "hello"
console.log(identity(42));                // 42 — type inferred automatically
```

## 7. Detailed Examples

**Generic interface — ek reusable "container" shape:**
```ts
interface ApiResponse<T> {
  success: boolean;
  data: T;
}

const userResponse: ApiResponse<{ name: string }> = {
  success: true,
  data: { name: "Yogesh" },
};

const numbersResponse: ApiResponse<number[]> = {
  success: true,
  data: [1, 2, 3],
};
```
Ye pattern real-world APIs mein bahut common hai — same `ApiResponse` shape, different `data` type, bina duplicate interfaces likhe.

**Utility Types — TypeScript built-in "type transformers":**
```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

// Partial<T> — saari properties optional bana deta hai (jaise "update" functions ke liye)
function updateUser(id: number, updates: Partial<User>) { /* ... */ }
updateUser(1, { name: "New Name" }); // Sirf 'name' de rahe hain, baaki optional hai

// Pick<T, Keys> — sirf specific properties choose karo
type PublicUser = Pick<User, "id" | "name">;
// { id: number; name: string }

// Omit<T, Keys> — specific properties exclude karo (Pick ka opposite)
type SafeUser = Omit<User, "password">;
// { id: number; name: string; email: string } — password hata diya!

// Record<Keys, Type> — ek object type jaha keys ek fixed set se hon
type UserRoles = Record<"admin" | "editor" | "viewer", boolean>;
// { admin: boolean; editor: boolean; viewer: boolean }
```

`Omit<User, "password">` jaisa pattern real-world APIs mein extremely common hai — jab tum database se `User` fetch karte ho (jisme `password` hash hoti hai) lekin frontend ko **kabhi bhi** password field nahi bhejni chahiye.

**Constrained generics — `T` ko restrict karna:**
```ts
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "Yogesh", age: 25 };
getProperty(user, "name");   // ✅ "Yogesh"
// getProperty(user, "email"); // ❌ Error — 'email' user ki property nahi hai
```
`K extends keyof T` ka matlab hai: `K` sirf `T` ki actual keys mein se ek ho sakta hai — isse typo-safe property access milta hai.

## 8. Mental Model

Generics ko socho **function ke liye ek "blank" type slot** ki tarah — jaise ek form mein "Type: _____" likha ho, aur jo bhi actual type tum fill karo, poora form (function ka return type, parameters) us type ke hisaab se automatically consistent ho jaaye. Utility types (`Partial`, `Pick`, `Omit`) ko socho **type ke liye ready-made transformation tools** — jaise Photoshop filters, jo ek existing type lekar usse ek modified version banate hain, bina manually dobara type likhe.

## 9. What Happens Internally?

Generics **compile-time hi** resolve ho jaate hain — jab tum `getFirstElement<number>([1,2,3])` call karte ho, TypeScript compiler `T` ko `number` se replace karke type-check karta hai, aur phir compiled JavaScript mein generic syntax **completely gayab** ho jaata hai (type erasure, jo Module 31-01 mein cover kiya). Runtime JavaScript ko generics ka koi idea hi nahi hota — ye purely ek compile-time type-safety mechanism hai.

## 10. Common Mistakes

- Generics ko overuse karna jaha simple union type kaafi ho — agar function sirf 2-3 fixed types accept karta hai, `string | number` likhna generics se zyada readable ho sakta hai.
- `Omit`/`Pick` mein property naam typo karna — TypeScript compile error dega, lekin beginners confuse ho jaate hain error message se.
- `any` ko generic ki jagah use karna "generics samajh nahi aaye" isliye — isse type-safety completely khatam ho jaati hai, jo generics ka poora purpose tha.

## 11. Edge Cases

- Multiple generic parameters possible hain: `function merge<T, U>(a: T, b: U): T & U`.
- Generic defaults bhi ho sakte hain: `interface Box<T = string> { value: T }` — agar type specify na karo, `string` default use hoga.

## 12. Real-World Usage

- API client functions jo different endpoints ke liye different response shapes handle karte hain (`ApiResponse<T>`).
- Redux/state-management jaise libraries — generic reducers, actions.
- Utility functions jo kisi bhi array type pe kaam karein (`getFirstElement<T>`, `groupBy<T>`).
- Form-handling libraries — `Partial<FormData>` se "draft" state represent karna jaha sab fields filled na ho.

## 13. Comparison With Related Concepts

| Approach | Reusability | Type Safety |
|----------|-------------|-------------|
| `any` parameter | High (accepts everything) | None — no checking at all |
| Union types (`string \| number`) | Limited to listed types | Full — but only for those exact types |
| Generics (`<T>`) | Full — works with any type | Full — type flows through consistently |

## 14. Practice Questions

1. Generic function likhne ka syntax kya hai?
2. `Partial<T>` aur `Pick<T, K>` mein kya farak hai?
3. `keyof T` kya karta hai?

## 15. Challenge

Ek generic function `filterArray<T>(arr: T[], predicate: (item: T) => boolean): T[]` likho jo `Array.prototype.filter` jaisa kaam kare, lekin manually implement karo, type-safe tareeke se. Phir isse numbers aur strings dono arrays pe test karo.

## 16. Interview Questions

**Q1: Generics ka main purpose kya hai — `any` se better kyun hai?**
A: Generics reusability aur type-safety dono ek saath dete hain. `any` type-checking completely disable kar deta hai — function kuch bhi accept/return kar sakta hai bina kisi guarantee ke. Generics ek "type variable" (`T`) use karte hain jo call-time pe specific type se replace hota hai — isse function reusable rehta hai (kisi bhi type ke saath kaam karta hai) lekin type-safety maintain rehti hai (input aur output types consistent rehte hain, jo `any` guarantee nahi karta).

**Q2: `Omit<User, "password">` real-world APIs mein kyun common pattern hai?**
A: Backend mein `User` type mein sensitive fields (jaise `password` hash) hoti hain jo database operations ke liye zaroori hain, lekin jo kabhi frontend ko response mein nahi jaani chahiye. `Omit` se ek naya type banaya jaata hai jo original `User` interface se hi derive hota hai (duplicate interface maintain nahi karna padta), minus sensitive fields — isse type-level pe guarantee milti hai ki accidentally password field kahi response mein include nahi ho sakti, aur agar `User` interface future mein change ho, `SafeUser` automatically update ho jaayega.

## 17. Chapter Summary

Generics (`<T>`) reusable, type-safe functions/interfaces banane ka tareeka hain — type call-time pe determine hota hai, compile-time pe hi resolve hoke erase ho jaata hai. Utility types (`Partial`, `Pick`, `Omit`, `Record`) existing types se naye types derive karne ke built-in tools hain, especially API responses aur update-functions ke liye useful. `K extends keyof T` se property-access ko typo-safe banaya ja sakta hai.

## 18. Revision Checklist

- [ ] Generic function/interface likh sakta hoon `<T>` syntax ke saath.
- [ ] `Partial`, `Pick`, `Omit`, `Record` ka use-case pata hai.
- [ ] Generics compile-time pe resolve hote hain, ye samajh gaya.
- [ ] `keyof` ka basic idea clear hai.

---

**Next:** [04-Migrating-JS-to-TS-and-Real-World-Usage.md](./04-Migrating-JS-to-TS-and-Real-World-Usage.md)
