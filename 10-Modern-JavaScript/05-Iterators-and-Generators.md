# Iterators and Generators

**Module:** 10-Modern-JavaScript
**Difficulty:** 🔴 Advanced
**Previous:** [04-Map-Set-WeakMap-WeakSet.md](./04-Map-Set-WeakMap-WeakSet.md)
**Next:** [06-Symbols.md](./06-Symbols.md)

---

## 1. Learning Objectives

- Iterator protocol samajhna — kya banata hai ek value "iterable".
- Custom iterables banana.
- Generator functions (`function*`, `yield`) se lazy sequences banana.

## 2. Prerequisites

Module 04 chapter 03 (`for...of`), Module 05 (functions).

## 3. Concept in Simple Hinglish

**Iterator protocol** wo "rulebook" hai jo decide karta hai ki koi value `for...of` se loop kiya ja sakta hai ya nahi (jaise arrays, strings, Maps, Sets). **Generators** ek special function type hain jo value **"pause and resume"** kar sakte hain — normal function ek baar chal ke poora khatam hota hai, generator beech mein ruk sakta hai aur baad mein wahi se continue kar sakta hai.

## 4. Technical Explanation

**Iterator Protocol:** Koi bhi object "iterable" hai agar uske paas `[Symbol.iterator]` method hai jo ek **iterator object** return karta hai — jisme `.next()` method ho jo `{ value, done }` shape ka object return kare.

**Generator Function:** `function*` syntax se define hoti hai. `yield` keyword function ko pause karta hai aur value return karta hai — jab dobara `.next()` call ho, wahi se continue hota hai.

## 5. Syntax

```js
// Custom iterator
const myIterable = {
  [Symbol.iterator]() {
    return {
      next() { return { value: ..., done: false/true }; }
    };
  }
};

// Generator
function* myGenerator() {
  yield value1;
  yield value2;
}
```

## 6. Basic Examples

```js
function* simpleGen() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = simpleGen();
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }
```

## 7. Detailed Examples

**Generators are iterable — use them directly with `for...of`:**
```js
function* colors() {
  yield "red";
  yield "green";
  yield "blue";
}

for (const color of colors()) {
  console.log(color); // red, green, blue
}

console.log([...colors()]); // ["red", "green", "blue"] — works with spread too!
```

**Building a custom iterable object (manual implementation):**
```js
const range = {
  start: 1,
  end: 5,
  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;
    return {
      next() {
        if (current <= end) {
          return { value: current++, done: false };
        }
        return { value: undefined, done: true };
      }
    };
  }
};

for (const num of range) {
  console.log(num); // 1 2 3 4 5
}
```
Ye same "range" ko generator se **bahut zyada simply** likha ja sakta hai:
```js
function* rangeGen(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}
console.log([...rangeGen(1, 5)]); // [1, 2, 3, 4, 5]
```
Generator syntax manual iterator protocol implementation ka **massive simplification** hai — yahi generators ka main value proposition hai.

**Infinite sequences — a use-case only generators handle elegantly:**
```js
function* infiniteCounter() {
  let n = 1;
  while (true) { // Infinite loop — but it's OK because of 'yield'!
    yield n++;
  }
}

const counter = infiniteCounter();
console.log(counter.next().value); // 1
console.log(counter.next().value); // 2
console.log(counter.next().value); // 3
// This never actually creates an infinite array — values are generated LAZILY, one at a time!
```
Ye "lazy evaluation" ka powerful example hai — infinite sequence banane ke bawajood, koi memory issue nahi hoti kyunki values **on-demand** generate hoti hain, sab ek saath nahi.

**Passing values INTO a generator via `.next(value)`:**
```js
function* conversation() {
  const name = yield "What's your name?";
  const age = yield `Hi ${name}, how old are you?`;
  return `${name} is ${age} years old.`;
}

const convo = conversation();
console.log(convo.next().value);        // "What's your name?"
console.log(convo.next("Ravi").value);   // "Hi Ravi, how old are you?"
console.log(convo.next(25).value);        // "Ravi is 25 years old."
```

## 8. Mental Model

Generator ko socho ek **bookmark wali kitab** — tum kitab padhte-padhte (execution) kisi bhi page pe ruk sakte ho (`yield`), bookmark laga sakte ho, aur baad mein exactly wahi se continue kar sakte ho (`.next()`), poori kitab ek saath padhne ke bajaye. Regular functions ek "one-shot movie" jaisi hain — start se end tak, bina pause ke.

## 9. What Happens Internally?

Generator function call hone par turant execute **nahi** hoti — ek **Generator object** return hota hai (jo iterator hai). Har `.next()` call function ko **agle `yield` tak** run karta hai, execution state (local variables, position) ko "freeze" kar deta hai us point pe. Engine internally is pause/resume mechanism ko ek special "generator state machine" ke through implement karta hai.

## 10. Common Mistakes

- Generator function ko call karke expect karna ki wo turant poora execute ho jaayega — actually generator object milta hai, `.next()` calls se hi execution hoti hai.
- `yield` ko `return` samajhna — `yield` pause karta hai (resume-able), `return` poori tarah khatam kar deta hai (generator `done: true` ho jaata hai).
- Custom iterator likhते waqt `done: true` ka final `{value: undefined}` return karna bhool jaana.

## 11. Edge Cases

```js
function* gen() {
  yield 1;
  return 2; // This value comes with done: true
  yield 3;  // Unreachable — never yielded
}
const g = gen();
console.log(g.next()); // { value: 1, done: false }
console.log(g.next()); // { value: 2, done: true } — return's value included, but marks completion
console.log(g.next()); // { value: undefined, done: true }
```

## 12. Real-World Usage

Generators async iteration patterns mein use hote hain (Module 13-14 se related — `async function*`), infinite/lazy data streams mein, aur Redux-Saga jaisi libraries mein complex async flows manage karne ke liye. Custom iterables tab useful hain jab tum apna khud ka data structure (jaise linked list, tree) banate ho jo `for...of` ke saath naturally kaam kare.

## 13. Comparison With Related Concepts

| Concept | Behavior |
|---------|-----------|
| Regular function | Runs completely, returns once |
| Generator function | Can pause (`yield`) and resume (`.next()`) multiple times |
| Iterator | Any object with a `.next()` method returning `{value, done}` |
| Iterable | Any object with `[Symbol.iterator]` returning an iterator |

## 14. Practice Questions

1. `yield` aur `return` mein generator ke context mein kya difference hai?
2. Ek object ko "iterable" banane ke liye kya zaroori hai?
3. Infinite generator memory issue kyun nahi create karta?

## 15. Challenge

Ek generator function `fibonacciGen()` likho jo Fibonacci sequence ke numbers infinitely yield kare. Use karke pehle 10 Fibonacci numbers print karo (`.next()` ko loop mein call karke ya `for...of` + `break` use karke).

## 16. Interview Questions

**Q1: Generator function kya hai aur normal function se kaise different hai?**
A: Generator function (`function*` syntax) ek special function hai jo **execution ko pause aur resume** kar sakti hai `yield` keyword ke through. Call hone par ye turant execute nahi hoti — ek **Generator object** (iterator) return karti hai. Har `.next()` call execution ko agle `yield` tak chalata hai, value return karta hai, aur state ko "freeze" kar deta hai. Normal functions ek baar call hone par completely run hoti hain aur ek hi baar value return karti hain, pause/resume support nahi karti.

**Q2: Kisi object ko "iterable" (jaise `for...of` ke saath usable) banane ke liye kya requirement hai?**
A: Object mein `[Symbol.iterator]` naam ki key honi chahiye jiski value ek function ho jo **iterator object** return kare. Wo iterator object ek `.next()` method rakhta ho jo har call pe `{ value, done }` shape ka object return kare — `done: false` jab tak values available hain, `done: true` jab sequence khatam ho jaaye. Generators automatically is protocol ko implement karte hain, isliye unhe manually likhne ki zaroorat nahi padti.

## 17. Chapter Summary

Iterator protocol define karta hai kaise koi value `for...of` ke saath loop-able banti hai — `[Symbol.iterator]` method jo `.next()` wala iterator return karta hai. Generators (`function*`, `yield`) is protocol ko elegant syntax ke saath implement karte hain, pause/resume execution enable karte hain, aur lazy/infinite sequences ko memory-efficient tareeke se possible banate hain.

## 18. Revision Checklist

- [ ] Iterator protocol (`[Symbol.iterator]`, `.next()`, `{value, done}`) ka structure yaad hai.
- [ ] Generator function ka pause/resume behavior clear hai.
- [ ] Infinite generator ka lazy-evaluation benefit samajh gaya.

---

**Next:** [06-Symbols.md](./06-Symbols.md)
