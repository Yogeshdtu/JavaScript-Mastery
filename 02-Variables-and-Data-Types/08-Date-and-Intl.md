# Date and Intl

**Module:** 02-Variables-and-Data-Types
**Difficulty:** 🟡 Beginner-Intermediate
**Previous:** [07-Regular-Expressions.md](./07-Regular-Expressions.md)
**Next:** [03-Operators/01-Arithmetic-and-Assignment-Operators.md](../03-Operators/01-Arithmetic-and-Assignment-Operators.md)

---

## 1. Learning Objectives

- `Date` object create aur manipulate karna.
- Timezone aur UTC vs local time ka farak samajhna.
- `Intl` API use karke locale-aware formatting (dates, numbers, currency) karna.

## 2. Prerequisites

Module 02, chapters 01-07.

## 3. Concept in Simple Hinglish

Har real-world app mein kabhi na kabhi date/time handle karna padta hai — "order kab place hua", "message kitni der pehle aaya", "price kis currency mein dikhana hai". JavaScript ka `Date` object isके liye hai, lekin ye **notoriously confusing** hai — especially timezones ke saath. `Intl` API ek zyada modern tool hai jo dates, numbers, aur currency ko user ki language/region ke hisaab se **automatically correctly format** karta hai, bina manually string-building kiye.

## 4. Technical Explanation

**`Date` object banane ke tarike:**
```js
const now = new Date();                          // current date/time
const specific = new Date("2024-01-15");           // ISO string se
const fromParts = new Date(2024, 0, 15);           // Year, Month(0-indexed!), Day
const fromTimestamp = new Date(1705276800000);     // milliseconds since Jan 1, 1970 (epoch)
```

⚠️ **Sabse bada gotcha: month 0-indexed hai.** `new Date(2024, 0, 15)` ka matlab hai **January** 15, 2024 — `0` = January, `11` = December.

**Common getter methods:**
```js
const d = new Date(2024, 0, 15, 10, 30);
d.getFullYear();   // 2024
d.getMonth();       // 0 (January) — 0-indexed!
d.getDate();        // 15 (day of month)
d.getDay();         // 1 (day of week, 0=Sunday)
d.getHours();       // 10
d.getMinutes();     // 30
d.getTime();        // timestamp (ms since epoch)
```

**`Intl` API — locale-aware formatting:**
```js
const date = new Date(2024, 0, 15);

// Date formatting
new Intl.DateTimeFormat("en-IN").format(date);       // "15/1/2024"
new Intl.DateTimeFormat("en-US").format(date);       // "1/15/2024"

// Currency formatting
new Intl.NumberFormat("en-IN", { style: "currency", currency: "INR" }).format(150000);
// "₹1,50,000.00" — Indian numbering system automatically!

new Intl.NumberFormat("en-US", { style: "currency", currency: "USD" }).format(150000);
// "$150,000.00"
```

## 5. Syntax

```js
new Date();
new Date(dateString);
new Date(year, monthIndex, day, hours, minutes, seconds);
Date.now();                                  // current timestamp (number)
new Intl.DateTimeFormat(locale, options).format(date);
new Intl.NumberFormat(locale, options).format(number);
```

## 6. Basic Examples

```js
const today = new Date();
console.log(today.getFullYear());               // e.g. 2026

const timestamp = Date.now();
console.log(typeof timestamp);                    // "number"

console.log(new Intl.NumberFormat("en-IN").format(1234567)); // "12,34,567"
```

## 7. Detailed Examples

**Age calculate karna:**
```js
function calculateAge(birthDate) {
  const today = new Date();
  let age = today.getFullYear() - birthDate.getFullYear();
  const monthDiff = today.getMonth() - birthDate.getMonth();

  // Agar birthday abhi tak nahi aaya is saal, age ek saal kam karo
  if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
    age--;
  }
  return age;
}
```

**"Time ago" jaisa relative time (social media style):**
```js
function timeAgo(pastDate) {
  const seconds = Math.floor((Date.now() - pastDate.getTime()) / 1000);
  if (seconds < 60) return "just now";
  const minutes = Math.floor(seconds / 60);
  if (minutes < 60) return `${minutes}m ago`;
  const hours = Math.floor(minutes / 60);
  if (hours < 24) return `${hours}h ago`;
  const days = Math.floor(hours / 24);
  return `${days}d ago`;
}
```

**`Intl.RelativeTimeFormat` — same cheez, built-in aur locale-aware:**
```js
const rtf = new Intl.RelativeTimeFormat("en", { numeric: "auto" });
console.log(rtf.format(-1, "day"));    // "yesterday"
console.log(rtf.format(-3, "hour"));   // "3 hours ago"
console.log(rtf.format(2, "day"));      // "in 2 days"
```

**Date comparison — direct `==`/`===` kaam nahi karta:**
```js
const d1 = new Date(2024, 0, 15);
const d2 = new Date(2024, 0, 15);
console.log(d1 === d2);              // false — different object references!
console.log(d1.getTime() === d2.getTime()); // true — timestamps compare karo
```

## 8. Mental Model

`Date` object ko socho ek **single number (timestamp) ka wrapper** — internally ye sirf "1970 se kitne milliseconds guzre" store karta hai. Saare getter methods (`getFullYear`, `getMonth`, etc.) us number ko **local timezone** ke hisaab se human-readable pieces mein todte hain. `Intl` ko socho ek **built-in translator** jo raw data (date, number) ko kisi bhi country/language ke convention mein present karta hai — bina tumhe khud string-formatting logic likhne ki zarurat.

## 9. What Happens Internally?

`Date` object internally ek single number store karta hai — UTC epoch se milliseconds. Jab tum `getFullYear()` ya `getHours()` call karte ho, engine us number ko **system ki local timezone settings** use karke convert karta hai. Isliye same `Date` object, different timezone wale computers pe, different local time dikha sakta hai — lekin `.getTime()` (raw timestamp) hamesha same rehta hai. `toISOString()` hamesha UTC mein output deta hai, isliye server-to-server data transfer ke liye ye safest format hai.

## 10. Common Mistakes

- Month ko 1-indexed samajhna — `new Date(2024, 1, 1)` February hai, January nahi.
- Do `Date` objects ko `===` se compare karna — hamesha `false` hoga (different references), `.getTime()` compare karo.
- Server pe date/time ko local format mein store karna — hamesha **UTC** (`toISOString()`) store karo database mein, display time pe hi local convert karo.
- `Date` object ko directly manipulate karke naya date banana bhoolna (jaise "add 5 days") — `Date` mutable hai, isliye original modify hone se bugs aa sakte hain agar reference kahi aur share ho rahi ho.

## 11. Edge Cases

- **Daylight Saving Time (DST):** Kuch countries mein saal mein ek ghanta aage/peeche hota hai — date arithmetic (jaise "add 24 hours") is wajah se unexpected results de sakti hai us din jab DST switch hota hai.
- **Invalid dates:** `new Date("not a date")` ek "Invalid Date" object banata hai jo `NaN` jaisa behave karta hai — hamesha `isNaN(date.getTime())` se validate karo.
- Month overflow automatically next year mein roll ho jaata hai: `new Date(2024, 12, 1)` automatically January 2025 ban jaata hai (koi error nahi aata).

## 12. Real-World Usage

- E-commerce: order timestamps, delivery estimates, currency display (region ke hisaab se).
- Social media: "5 minutes ago" jaisi relative timestamps.
- Analytics dashboards: date-range filtering, time-series charts.
- Internationalized apps: different countries ke users ko unki language/currency mein content dikhana.
- Booking systems: timezone-aware scheduling (flights, meetings across regions).

## 13. Comparison With Related Concepts

| Approach | Best For | Limitation |
|----------|----------|------------|
| Native `Date` | Basic date storage/arithmetic | Confusing API, mutable, timezone handling manual |
| `Intl.DateTimeFormat` / `NumberFormat` | Locale-aware display formatting | Sirf formatting ke liye, arithmetic ke liye nahi |
| Libraries (`date-fns`, `Day.js`, `Luxon`) | Complex date math, timezone-heavy apps | Extra dependency, but far more ergonomic API |

**Production tip:** Simple formatting/display ke liye native `Date` + `Intl` kaafi hai. Complex date arithmetic (timezones, recurring events, business-day calculations) wale apps mein `date-fns` ya `Luxon` jaisi library use karna industry-standard practice hai — native `Date` API ki quirks bahut saare subtle bugs paida karti hain.

## 14. Practice Questions

1. `new Date(2024, 0, 1)` kaunsa month represent karta hai?
2. Do dates compare karne ka sahi tareeka kya hai?
3. `Intl.NumberFormat` currency format karne ke liye kaunse options leta hai?

## 15. Challenge

Ek function `formatOrderDate(date, locale, currency, amount)` likho jo `Intl.DateTimeFormat` aur `Intl.NumberFormat` use karke ek string return kare jaise: `"Order placed on 15/1/2024 for ₹1,500.00"` — locale aur currency parameter se dynamically formatted.

## 16. Interview Questions

**Q1: `Date` object mein timestamp aur local-time-methods ka farak kya hai?**
A: Internally `Date` sirf ek number store karta hai — UTC epoch se milliseconds (`.getTime()` se access hota hai, timezone-independent). `getFullYear()`, `getHours()` jaise methods us number ko system ki **local timezone** ke hisaab se convert karke human-readable value dete hain — isliye ye system ki timezone settings pe depend karte hain, jabki raw timestamp universal hai. Isi wajah se server data hamesha UTC (`toISOString()`) mein store karna best practice hai.

**Q2: `Intl` API ka use karna native string concatenation se better kyun hai?**
A: `Intl` API locale-specific formatting rules ko automatically handle karta hai — number grouping (jaise Indian `1,50,000` vs Western `150,000`), currency symbols, date order (DD/MM vs MM/DD), aur pluralization rules — ye sab manually string-concatenation se implement karna error-prone aur incomplete hota hai. `Intl` browser-native, well-tested, aur maintenance-free hai.

## 17. Chapter Summary

`Date` internally ek epoch-timestamp store karta hai; local getters us number ko system timezone ke hisaab se convert karte hain. Month 0-indexed hai — common bug source. Dates ko `===` se compare mat karo, `.getTime()` use karo. `Intl.DateTimeFormat`/`NumberFormat`/`RelativeTimeFormat` locale-aware formatting ke liye modern, reliable tools hain — complex date-math ke liye `date-fns`/`Luxon` jaisi library better hai.

## 18. Revision Checklist

- [ ] Month 0-indexed hone ka gotcha yaad hai.
- [ ] Date comparison ka sahi tareeka (`.getTime()`) pata hai.
- [ ] UTC vs local time ka farak samajh gaya.
- [ ] `Intl.DateTimeFormat` aur `Intl.NumberFormat` ka basic use-case pata hai.

---

**Module 02 Complete!** Next Module: [03-Operators/01-Arithmetic-and-Assignment-Operators.md](../03-Operators/01-Arithmetic-and-Assignment-Operators.md)
