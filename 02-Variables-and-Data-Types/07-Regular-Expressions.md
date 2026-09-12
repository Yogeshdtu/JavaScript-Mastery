# Regular Expressions

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [06-Type-Checking.md](./06-Type-Checking.md)
**Next:** [08-Date-and-Intl.md](./08-Date-and-Intl.md)

---

## 1. Learning Objectives

- Regular expressions (regex) create karna aur samajhna — literal syntax aur `RegExp` constructor dono.
- String methods ke saath regex use karna: `test`, `match`, `matchAll`, `replace`, `replaceAll`, `split`.
- Common regex patterns aur flags (`g`, `i`, `m`, `s`) practically use karna.

## 2. Prerequisites

Module 02, chapters 01-06 (specially strings/primitive types ka basic idea).

## 3. Concept in Simple Hinglish

Regex ek **pattern-matching language** hai jo strings ke andar specific patterns dhundhne, check karne, ya replace karne ke liye use hota hai. Socho tumhe check karna hai ki koi email valid format mein hai ya nahi, ya kisi text se saare numbers nikalne hain — normal string methods (`includes`, `indexOf`) se ye mushkil hai kyunki wo exact text dhundhte hain, pattern nahi. Regex tumhe kehne deta hai: "mujhe koi bhi cheez chahiye jo is shape ki ho" — jaise "3 digits, phir ek dash, phir 4 digits."

## 4. Technical Explanation

Regex ek object hota hai jo ek pattern represent karta hai. Do tarike se banta hai:

```js
// Literal syntax (recommended jab pattern fixed ho)
const pattern1 = /hello/;

// Constructor syntax (recommended jab pattern dynamic ho, variable se banta ho)
const pattern2 = new RegExp("hello");
```

**Flags** pattern ke behavior ko modify karte hain:

| Flag | Meaning |
|------|---------|
| `g` | Global — saare matches dhundo, sirf pehla nahi |
| `i` | Case-insensitive — `A` aur `a` same treat karo |
| `m` | Multiline — `^` aur `$` har line ke start/end pe match karein |
| `s` | Dotall — `.` newline (`\n`) ko bhi match kare |

**Common pattern building blocks:**

```js
/\d/       // koi bhi digit (0-9)
/\w/       // word character (letters, digits, underscore)
/\s/       // whitespace (space, tab, newline)
/./        // koi bhi character (newline ke alawa, jab tak 's' flag na ho)
/[abc]/    // a, b, ya c mein se koi ek
/[^abc]/   // a, b, c ke alawa koi bhi
/a+/       // ek ya zyada 'a'
/a*/       // zero ya zyada 'a'
/a?/       // zero ya ek 'a'
/a{2,4}/   // 2 se 4 'a' ke beech
/^abc/     // string 'abc' se start honi chahiye
/abc$/     // string 'abc' pe end honi chahiye
```

## 5. Syntax

```js
regex.test(string);              // true/false return karta hai
string.match(regex);              // matches ka array (ya null)
string.matchAll(regex);           // sabhi matches ka iterator (g flag zaroori)
string.replace(regex, replacement);
string.replaceAll(regex, replacement); // regex ke saath 'g' flag zaroori hai
string.split(regex);
```

## 6. Basic Examples

```js
const pattern = /\d+/;
console.log(pattern.test("Order 42"));       // true — digit mila
console.log("Order 42".match(pattern));       // ["42", index: 6, ...]
console.log("a1b2c3".replace(/\d/g, "#"));   // "a#b#c#"
console.log("2024-01-15".split("-"));         // ["2024", "01", "15"]
```

## 7. Detailed Examples

**Email format validate karna (basic version, production-grade nahi):**
```js
function isValidEmailFormat(email) {
  const pattern = /^[\w.+-]+@[\w-]+\.[a-zA-Z]{2,}$/;
  return pattern.test(email);
}

console.log(isValidEmailFormat("user@example.com")); // true
console.log(isValidEmailFormat("not-an-email"));       // false
```

**Named capture groups se structured data nikalna:**
```js
const dateStr = "2024-01-15";
const pattern = /^(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})$/;
const match = dateStr.match(pattern);

console.log(match.groups.year);  // "2024"
console.log(match.groups.month);  // "01"
console.log(match.groups.day);    // "15"
```

**`matchAll` se saare matches with position:**
```js
const text = "Prices: $10, $25, $99";
const matches = [...text.matchAll(/\$(\d+)/g)];
console.log(matches.map(m => m[1])); // ["10", "25", "99"]
```

**Dynamic pattern (user input se) — `RegExp` constructor zaroori:**
```js
function highlightWord(text, word) {
  const pattern = new RegExp(word, "gi"); // variable pattern isliye constructor use kiya
  return text.replace(pattern, `**${word}**`);
}
console.log(highlightWord("Cats and cats everywhere", "cat"));
// "**cat**s and **cat**s everywhere"
```

## 8. Mental Model

Regex ko socho ek **template/stencil** ki tarah jo string ke upar rakha jaata hai — jaha jaha stencil ka shape match karta hai, wahi "match" hai. `test()` sirf poochta hai "kahi match hua?" (haan/na), `match()`/`matchAll()` batate hain "kaha aur kya match hua", aur `replace()` matched hisse ko kisi aur cheez se badal deta hai.

## 9. What Happens Internally?

JavaScript engine regex pattern ko ek **finite state machine** mein compile karta hai — string ke characters ko ek-ek karke process karta hai, states ke beech transition karta hai jab tak match mil jaaye ya pattern fail ho jaaye. Complex patterns (especially nested quantifiers jaise `(a+)+`) worst case mein **exponential time** le sakte hain — isse "catastrophic backtracking" kehte hain, jo bade inputs pe server ko freeze kar sakta hai (ReDoS — Regular Expression Denial of Service).

## 10. Common Mistakes

- `.replace()` bina `g` flag ke sirf **pehla match** replace karta hai — saare replace karne ke liye `g` flag ya `replaceAll()` chahiye.
- `.` character `\n` (newline) match nahi karta by default — multiline text ke liye `s` flag chahiye.
- Special characters (`.`, `*`, `+`, `?`, `(`, `)`, `[`, `]`) ko literally match karne ke liye escape (`\.`) karna bhoolna — `1.5` ka pattern `/1.5/` likhne se `1` + koi bhi character + `5` match hoga, sirf literal dot nahi.
- Complex regex likhna jo readable na ho — comments ya named groups se readability improve karo.

## 11. Edge Cases

- `g` flag wala regex object **stateful** hota hai — `lastIndex` property track karta hai. Same regex object ko multiple baar `.test()` karne se alternating `true`/`false` mil sakta hai agar tum bhool jao ki `lastIndex` reset nahi hua.
```js
const re = /a/g;
console.log(re.test("aaa")); // true (lastIndex = 1)
console.log(re.test("aaa")); // true (lastIndex = 2)
console.log(re.test("aaa")); // true (lastIndex = 3)
console.log(re.test("aaa")); // false! lastIndex reset ho gaya, restart
```
- User-provided strings ko dynamic regex mein daalne se pehle special characters escape karna zaroori hai, warna unexpected pattern behavior ya security issues ho sakte hain.

## 12. Real-World Usage

- Form validation (email, phone number, password strength patterns).
- Log files se specific data extract karna (timestamps, error codes).
- Search-and-replace features (find-and-replace in editors, text processing tools).
- URL routing patterns (Express jaise frameworks route paths ko internally regex mein convert karte hain).
- Data sanitization — unwanted characters remove karna user input se.

## 13. Comparison With Related Concepts

| Approach | Best For | Limitation |
|----------|----------|------------|
| String methods (`includes`, `indexOf`) | Exact text search | Patterns/variations handle nahi karte |
| Regular Expressions | Pattern matching, validation, extraction | Complex patterns padhna mushkil; performance risk (ReDoS) |
| Dedicated validation libraries | Production-grade email/phone validation | Extra dependency, but zyada robust edge-case handling |

**Production tip:** Email jaise complex formats ke liye, full RFC-compliant regex likhna almost impossible hai — production apps mein aksar simple regex + server-side verification (jaise confirmation email bhejna) combine kiya jaata hai.

## 14. Practice Questions

1. `/g` flag ka kya effect hota hai `replace()` pe?
2. `\d`, `\w`, aur `\s` kya represent karte hain?
3. Named capture groups kaise define aur access karte hain?

## 15. Challenge

Ek function `extractHashtags(text)` likho jo kisi string se saare hashtags (`#word`) ek array mein return kare, bina `#` symbol ke. Example: `extractHashtags("Loving #javascript and #coding")` → `["javascript", "coding"]`.

## 16. Interview Questions

**Q1: `test()` aur `match()` mein kya farak hai?**
A: `test()` ek regex method hai jo boolean (`true`/`false`) return karta hai — sirf ye batata hai ki match hua ya nahi. `match()` ek string method hai jo actual matched text (aur additional info jaise index, groups) ka array return karta hai, ya koi match na ho to `null`.

**Q2: Catastrophic backtracking kya hai aur kaise avoid karein?**
A: Ye tab hota hai jab regex pattern mein nested quantifiers hote hain (jaise `(a+)+b`) aur input string match fail karti hai — engine exponentially many combinations try karta hai, jisse execution time seconds ya minutes tak badh sakta hai bade inputs pe. Isse avoid karne ke liye: nested quantifiers se bacho, patterns ko simple rakho, aur user-input-based dynamic patterns ko carefully validate karo. Production mein regex-safety-checking libraries bhi use ki jaati hain.

## 17. Chapter Summary

Regex pattern-matching ke liye powerful tool hai — `test()`, `match()`, `matchAll()`, `replace()` string operations ke saath use hota hai. Flags (`g`, `i`, `m`, `s`) behavior modify karte hain. `g` flag wale regex objects stateful hote hain (`lastIndex`), aur complex nested patterns performance risk (catastrophic backtracking) la sakte hain.

## 18. Revision Checklist

- [ ] Literal aur constructor syntax dono se regex bana sakta hoon.
- [ ] `test`, `match`, `matchAll`, `replace`, `replaceAll` ka use-case pata hai.
- [ ] Common patterns (`\d`, `\w`, `\s`, quantifiers) yaad hain.
- [ ] Catastrophic backtracking ka risk samajh gaya.

---

**Next:** [08-Date-and-Intl.md](./08-Date-and-Intl.md)
