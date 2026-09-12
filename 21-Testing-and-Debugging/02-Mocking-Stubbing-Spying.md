# Mocking, Stubbing, and Spying

**Module:** 21-Testing-and-Debugging
**Difficulty:** 🔴 Advanced
**Previous:** [01-Testing-Fundamentals.md](./01-Testing-Fundamentals.md)
**Next:** [03-Debugging-Tools-and-Techniques.md](./03-Debugging-Tools-and-Techniques.md)

---

## 1. Learning Objectives

- Test doubles (mocks, stubs, spies) ka concept aur difference samajhna.
- External dependencies (jaise API calls) ko mock karna.
- Function calls ko spy/verify karna.

## 2. Prerequisites

[01-Testing-Fundamentals.md](./01-Testing-Fundamentals.md)

## 3. Concept in Simple Hinglish

Jab tum ek function test karte ho jo **external dependencies** pe depend karta hai (jaise API call, database, ya random values), tum nahi chahte ki test actually real API ko hit kare (slow, unreliable, costs money). **Test doubles** (mocks, stubs, spies) fake versions hain in dependencies ke jo tumhe controlled, predictable testing environment dete hain.

## 4. Technical Explanation

**Stub:** Ek fake function jo **predetermined value** return karta hai — real logic ko replace karta hai.

**Mock:** Ek fake function/object jo stub ki tarah predetermined behavior bhi deta hai, aur **verify bhi karta hai** ki wo kaise call hua (kitni baar, kaunse arguments ke saath).

**Spy:** Ek wrapper jo **real function ko actually call hone deta hai**, lekin uski calls ko track/record karta hai (jaise kitni baar call hua) bina behavior change kiye.

## 5. Syntax

```js
// Jest-style mocking
const mockFn = jest.fn(); // Creates a mock function
mockFn.mockReturnValue(42); // Stub-like behavior — always returns 42

jest.spyOn(object, "methodName"); // Spy on an existing method

expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith(arg1, arg2);
expect(mockFn).toHaveBeenCalledTimes(3);
```

## 6. Basic Examples

```js
const mockCallback = jest.fn();

[1, 2, 3].forEach(mockCallback);

expect(mockCallback).toHaveBeenCalledTimes(3);
expect(mockCallback).toHaveBeenCalledWith(1, 0, [1, 2, 3]);
```

## 7. Detailed Examples

**Mocking an external API call (connects to Module 16's Fetch API):**
```js
// userService.js
async function getUser(id) {
  const response = await fetch(`https://api.example.com/users/${id}`);
  return response.json();
}

// userService.test.js
global.fetch = jest.fn(); // Replace the real fetch with a mock

test("getUser returns user data correctly", async () => {
  fetch.mockResolvedValueOnce({
    json: async () => ({ id: 1, name: "Test User" })
  });

  const user = await getUser(1);

  expect(user).toEqual({ id: 1, name: "Test User" });
  expect(fetch).toHaveBeenCalledWith("https://api.example.com/users/1");
});
```
Is test mein **real network call kabhi nahi hoti** — `fetch` completely mock kar diya gaya hai. Ye test **fast, reliable, aur offline-runnable** hai, kyunki ye real API pe depend nahi karta.

**Stubbing — replacing a dependency with fixed behavior:**
```js
function getGreeting(getCurrentHour) { // Dependency injected as a parameter (a testable design!)
  const hour = getCurrentHour();
  if (hour < 12) return "Good morning";
  if (hour < 18) return "Good afternoon";
  return "Good evening";
}

test("returns 'Good morning' before noon", () => {
  const stubGetHour = () => 9; // Stub — always returns 9, regardless of actual time
  expect(getGreeting(stubGetHour)).toBe("Good morning");
});

test("returns 'Good evening' after 6pm", () => {
  const stubGetHour = () => 20;
  expect(getGreeting(stubGetHour)).toBe("Good evening");
});
```
Notice karo — `getGreeting` ko is tarah design kiya gaya hai ki `getCurrentHour` ek **parameter** hai, hardcoded `Date.now()` nahi — ye "dependency injection" pattern testing ko bahut aasan banata hai (Module 25 - Design Patterns mein related concepts).

**Spying — observing calls without changing behavior:**
```js
const mathUtils = {
  add(a, b) {
    return a + b;
  }
};

test("add is called with correct arguments", () => {
  const spy = jest.spyOn(mathUtils, "add"); // Wraps the REAL function, doesn't replace it

  const result = mathUtils.add(2, 3);

  expect(result).toBe(5); // Real function still executed correctly!
  expect(spy).toHaveBeenCalledWith(2, 3); // But we can verify HOW it was called
  spy.mockRestore(); // Clean up — restore original behavior
});
```

**Mocking timers (connects to Module 13-15's async concepts):**
```js
jest.useFakeTimers(); // Replace real timers with controllable fake ones

test("callback runs after delay", () => {
  const callback = jest.fn();
  setTimeout(callback, 1000);

  expect(callback).not.toHaveBeenCalled(); // Not called yet — time hasn't "passed"

  jest.advanceTimersByTime(1000); // Fast-forward time WITHOUT actually waiting 1 second!

  expect(callback).toHaveBeenCalled(); // Now it has been called
});
```
Fake timers se tests **instantly** run hote hain, chahe actual code mein multi-second delays ho — real time wait karne ki zaroorat nahi.

## 8. Mental Model

Mocks/stubs ko socho **movie sets** — jab film mein "restaurant scene" shoot karna ho, actual restaurant use karne ke bajaye (real API — slow, unpredictable, expensive), ek controlled studio-set banaya jaata hai (mock) jo exactly wahi dikhta/behave karta hai jo scene ke liye chahiye. Spies ko socho **hidden cameras** — real activity actually hoti hai, sirf observe/record ki jaati hai bina interfere kiye.

## 9. What Happens Internally?

Jest jaisi frameworks JavaScript ke dynamic nature (functions first-class values hain — Module 05, chapter 01 se yaad karo) ka use karke original function-references ko temporarily fake implementations se replace kar dete hain, calls ko internally ek array mein track karte hue (`mock.calls`). `spyOn` similar hai, but original implementation ko bhi preserve/call karta hai by default.

## 10. Common Mistakes

- Har test ke baad mocks/spies ko reset/restore karna bhool jaana — ek test ke mocks doosre test ko affect kar sakte hain.
- Real API calls ko test mein directly hit karna (mock kiye bina) — tests slow, flaky, aur unreliable ban jaate hain.
- Bahut zyada mocking karna — agar sab kuch mock ho jaaye, test actually kuch bhi real verify nahi kar raha hota.

## 11. Edge Cases

`jest.mock()` poore modules ko automatically mock karne ke liye use hota hai — ye especially useful hai jab ek function bahut saari nested dependencies use karta hai jinhe manually stub karna impractical hoga.

## 12. Real-World Usage

External API calls, database operations, file-system operations, aur timers — ye sab common cheezein hain jo tests mein mock ki jaati hain, taaki tests fast, reliable, aur real external systems se independent rahen.

## 13. Comparison With Related Concepts

| Test Double | Behavior | Verification |
|--------------|-------------|-------------------|
| Stub | Returns fixed/predetermined value | Usually not verified |
| Mock | Returns fixed value + tracks calls | Explicitly verified (`toHaveBeenCalled`, etc.) |
| Spy | Calls real function + tracks calls | Explicitly verified, real behavior preserved |

## 14. Practice Questions

1. Mock aur Spy mein kya difference hai (real function call hoti hai ya nahi)?
2. Real API calls ko tests mein mock karna kyun important hai?
3. `jest.useFakeTimers()` ka use-case kya hai?

## 15. Challenge

Ek function `sendWelcomeEmail(emailService, userEmail)` likho jo `emailService.send(userEmail, message)` call kare. Ek test likho jo mock `emailService` use kare aur verify kare ki `send` sahi arguments ke saath call hua.

## 16. Interview Questions

**Q1: Mocking, Stubbing, aur Spying mein kya difference hai?**
A: **Stub** ek dependency ko replace karta hai jo sirf **predetermined values return karta hai**, bina call-tracking ki concern ke. **Mock** stub ki tarah predetermined behavior deta hai, lekin isके saath ye **calls ko bhi track/verify** karta hai (kitni baar, kaunse arguments ke saath call hua). **Spy** original/real function ko **actually execute hone deta hai**, lekin uski calls ko observe/record karta hai bina behavior change kiye — real logic preserve rehti hai, sirf monitoring add hoti hai. Teeno "test doubles" ki category mein aate hain, jo real dependencies ko controlled, testable versions se replace/observe karte hain.

**Q2: Tests mein external API calls ko mock karna kyun zaroori hai, real API call karne ke bajaye?**
A: Real API calls pe depend karne wale tests kai problems create karte hain: (1) **Slow** — network requests test-suite ko significantly slow kar sakti hain; (2) **Unreliable/Flaky** — network issues, API downtime, ya rate-limiting tests ko randomly fail kar sakte hain bina code mein kisi bug ke; (3) **Side effects** — real API calls actual data create/modify/delete kar sakti hain (jaise ek real order place ho jaana test ke dauraan); (4) **Cost** — kuch APIs paid hain, har test-run pe unhe hit karna expensive ho sakta hai. Mocking in issues ko eliminate karta hai — tests fast, deterministic, aur completely isolated ban jaate hain real external systems se.

## 17. Chapter Summary

Test doubles (mocks, stubs, spies) real dependencies ko controlled, testable versions se replace karte hain. Stubs fixed values return karte hain, mocks isके saath calls verify karte hain, spies real function-execution ko preserve karte hue observe karte hain. Ye external APIs, timers, databases ko test karte waqt fast, reliable tests banane ke liye essential hain.

## 18. Revision Checklist

- [ ] Mock vs Stub vs Spy ka difference clear hai.
- [ ] API-call mocking pattern likh sakta hoon.
- [ ] Fake timers ka use-case samajh gaya.

---

**Next:** [03-Debugging-Tools-and-Techniques.md](./03-Debugging-Tools-and-Techniques.md)
