# Testing Fundamentals

**Module:** 21-Testing-and-Debugging
**Difficulty:** 🟠 Advanced
**Previous:** [20-Databases/04-Database-Security.md](../20-Databases/04-Database-Security.md)
**Next:** [02-Mocking-Stubbing-Spying.md](./02-Mocking-Stubbing-Spying.md)

---

## 1. Learning Objectives

- Unit, Integration, aur E2E testing ka difference samajhna.
- Assertions likhna (Jest-style syntax ke saath).
- TDD (Test-Driven Development) ka basic concept samajhna.

## 2. Prerequisites

Modules 01-20 complete (testing sabhi tarah ke code — functions, APIs, aur bahut kuch — pe apply hoti hai, isliye poora course ka context helpful hai).

## 3. Concept in Simple Hinglish

**Testing** matlab apne code ko **automatically verify** karna ki wo expected tareeke se kaam kar raha hai — manually browser mein click-click karke check karne ke bajaye, code likhkar check karna. Ye confidence deta hai ki naye changes purane features ko break nahi kar rahe (regression prevention).

## 4. Technical Explanation

**3 Levels of Testing:**
1. **Unit Testing:** Individual functions/components ko isolation mein test karna (jaise ek single `add()` function).
2. **Integration Testing:** Multiple parts ko **saath mein** test karna (jaise API endpoint jo database se interact karta hai).
3. **End-to-End (E2E) Testing:** Poori application ko user ki tarah test karna (jaise browser automation se pura signup-flow test karna).

**Assertion:** Ek statement jo verify karta hai ki actual result expected result ke barabar hai (`expect(result).toBe(expected)`).

## 5. Syntax

```js
// Jest-style syntax (most popular JavaScript testing framework)
test("description of what is being tested", () => {
  expect(actualValue).toBe(expectedValue);
});

describe("group of related tests", () => {
  test("specific test case", () => { /* ... */ });
});
```

## 6. Basic Examples

```js
function add(a, b) {
  return a + b;
}

test("add() should correctly add two numbers", () => {
  expect(add(2, 3)).toBe(5);
});
```

## 7. Detailed Examples

**A complete unit test suite for a function (revisiting Module 05's functions):**
```js
function divide(a, b) {
  if (b === 0) {
    throw new Error("Cannot divide by zero");
  }
  return a / b;
}

describe("divide()", () => {
  test("divides two positive numbers correctly", () => {
    expect(divide(10, 2)).toBe(5);
  });

  test("handles negative numbers", () => {
    expect(divide(-10, 2)).toBe(-5);
  });

  test("throws an error when dividing by zero", () => {
    expect(() => divide(10, 0)).toThrow("Cannot divide by zero");
  });

  test("returns a decimal for non-even division", () => {
    expect(divide(7, 2)).toBeCloseTo(3.5); // toBeCloseTo for floating point comparisons!
  });
});
```

**Common Jest matchers — the assertion vocabulary:**
```js
expect(value).toBe(5);                  // Strict equality (===)
expect(value).toEqual({ a: 1 });          // Deep equality (for objects/arrays)
expect(value).toBeTruthy();                // Module 09, chapter 01 concept applied!
expect(value).toBeFalsy();
expect(value).toBeNull();
expect(value).toBeUndefined();
expect(array).toContain(item);
expect(array).toHaveLength(3);
expect(fn).toThrow();
expect(value).toBeGreaterThan(10);
```

**`toBe` vs `toEqual` — a critical distinction (connects to Module 02, chapter 05):**
```js
test("toBe fails for objects with same content but different reference", () => {
  const obj1 = { name: "Test" };
  const obj2 = { name: "Test" };

  // expect(obj1).toBe(obj2);   // ❌ FAILS — different object references (Module 02, chapter 05!)
  expect(obj1).toEqual(obj2);   // ✅ PASSES — deep-compares the actual content
});
```

**Testing asynchronous code (connects to Module 13-14):**
```js
async function fetchUserName(id) {
  const user = await getUserFromDatabase(id); // Assume this is an async function
  return user.name;
}

test("fetchUserName returns the correct name", async () => { // Note: test function is async!
  const name = await fetchUserName(1); // await the result before asserting
  expect(name).toBe("Aarav");
});
```

**Basic TDD (Test-Driven Development) cycle — "Red, Green, Refactor":**
```js
// STEP 1 (Red): Write a FAILING test first, for functionality that doesn't exist yet
test("isPalindrome should return true for palindromes", () => {
  expect(isPalindrome("racecar")).toBe(true); // isPalindrome doesn't exist yet — this FAILS
});

// STEP 2 (Green): Write the MINIMUM code to make the test pass
function isPalindrome(str) {
  return str === str.split("").reverse().join("");
}
// Now the test PASSES

// STEP 3 (Refactor): Improve the code while keeping tests passing
function isPalindromeRefactored(str) {
  const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, "");
  return cleaned === cleaned.split("").reverse().join("");
}
// Re-run tests — should still pass, now with better/more robust logic
```

## 8. Mental Model

Testing ko socho ek **safety net neeche ek tightrope walker ke** — bina net (tests) ke, ek galti (bug) disaster ban sakti hai. Net ke saath, tum confidently naye moves (code changes) try kar sakte ho, kyunki tumhe pata hai agar kuch galat hua, net (test-failure) turant pakad legi, production mein pahunchne se pehle.

## 9. What Happens Internally?

Testing framework (jaise Jest) tumhari test files ko run karta hai, har `test()`/`it()` block ko individually execute karta hai, `expect()` assertions ko evaluate karta hai, aur pass/fail results ko report karta hai — usually ek summary ke saath (kitne pass, kitne fail, kitna time laga).

## 10. Common Mistakes

- Sirf "happy path" test karna, edge cases (empty input, errors, boundary values) ignore karna.
- `toBe` use karna objects/arrays compare karte waqt jab `toEqual` chahiye tha.
- Async tests mein `await` bhool jaana — test "pass" ho sakta hai galti se, bina actually kuch verify kiye.

## 11. Edge Cases

Flaky tests (jo kabhi pass, kabhi fail hote hain bina code change ke) usually timing-issues (async code properly awaited nahi) ya external-dependency issues (network calls jo mock nahi ki gayi — next chapter) ki wajah se hote hain.

## 12. Real-World Usage

Professional software teams testing ko development-process ka core part maante hain — CI/CD pipelines (automated deployment) mein tests automatically run hote hain, aur agar koi test fail ho, deployment block ho jaata hai. Ye "regression bugs" (purana feature accidentally break hona) se protect karta hai.

## 13. Comparison With Related Concepts

| Test Type | Scope | Speed |
|-----------|-------|-------|
| Unit | Single function/component | Fast |
| Integration | Multiple components together | Medium |
| E2E | Entire application, user-perspective | Slow |

## 14. Practice Questions

1. `toBe` aur `toEqual` mein kya difference hai?
2. Unit test aur Integration test mein kya difference hai?
3. TDD ka "Red, Green, Refactor" cycle kya hai?

## 15. Challenge

Module 05 se `factorial(n)` function (recursion wala example) lo, aur uske liye 4 tests likho: normal case, `factorial(0)` edge case, `factorial(1)` edge case, aur negative-number handling (agar tum wo behavior implement karo).

## 16. Interview Questions

**Q1: Unit testing, Integration testing, aur E2E testing mein kya difference hai?**
A: **Unit testing** individual functions/components ko **isolation mein** test karta hai (dependencies mock ki jaati hain, next chapter) — fast aur focused. **Integration testing** multiple components ko **saath milke** test karta hai (jaise ek API route jo actually database se baat karta hai) — real interactions verify karta hai, thoda slower. **E2E (End-to-End) testing** poori application ko **user ke perspective se** test karta hai (browser automation ke saath, jaise login karke checkout tak poora flow) — sabse realistic, but sabse slow aur maintain karne mein complex. Ek healthy test-suite generally teeno types ka mix rakhta hai, unit tests sabse zyada mein.

**Q2: `toBe` aur `toEqual` Jest matchers mein kya difference hai?**
A: `toBe` **strict equality (`===`)** use karta hai — primitives ke liye theek hai, lekin objects/arrays ke liye ye **reference-equality** check karta hai (Module 02, chapter 05 se yaad karo) — do alag objects same content ke saath bhi `toBe` se match nahi karenge. `toEqual` **deep equality** check karta hai — objects/arrays ke andar ki **actual values** compare karta hai, reference ignore karke. Isliye objects/arrays test karte waqt `toEqual` use karna chahiye, `toBe` sirf primitives (numbers, strings, booleans) ke liye reliable hai.

## 17. Chapter Summary

Testing code ko automatically verify karne ka tareeka hai — Unit (isolated), Integration (combined components), aur E2E (full application) levels pe. Jest jaisi frameworks `test()`/`expect()` syntax provide karti hain assertions likhne ke liye. TDD ek development-approach hai jaha tests code se pehle likhe jaate hain (Red-Green-Refactor cycle).

## 18. Revision Checklist

- [ ] Unit vs Integration vs E2E testing ka scope-difference clear hai.
- [ ] `toBe` vs `toEqual` ka reference-vs-deep-equality difference yaad hai.
- [ ] Async code ko test karne ka pattern (`async test`, `await`) samajh gaya.

---

**Next:** [02-Mocking-Stubbing-Spying.md](./02-Mocking-Stubbing-Spying.md)
