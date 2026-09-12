# Debugging Tools and Techniques

**Module:** 21-Testing-and-Debugging
**Difficulty:** 🟠 Advanced
**Previous:** [02-Mocking-Stubbing-Spying.md](./02-Mocking-Stubbing-Spying.md)
**Next:** [22-Performance/01-Big-O-Basics.md](../22-Performance/01-Big-O-Basics.md)

---

## 1. Learning Objectives

- Browser DevTools aur Node.js debugger effectively use karna.
- `console` object ke advanced methods jaanna (sirf `console.log` nahi).
- Stack traces ko systematically read karke root-cause dhundna.

## 2. Prerequisites

Modules 01-21 (debugging skills poore course mein applicable hain).

## 3. Concept in Simple Hinglish

Debugging matlab hai **systematically dhundna ki code galat kyun behave kar raha hai** — sirf random `console.log`s daal dena "guessing" hai, real debugging **tools aur techniques** use karke methodically problem ko isolate karna hai.

## 4. Technical Explanation

**Browser DevTools (`F12`):**
- **Console tab:** Logs dekhna, JavaScript directly execute karna.
- **Sources tab:** Breakpoints set karna, code ko line-by-line step through karna.
- **Network tab:** HTTP requests/responses inspect karna (Module 16 se connected).

**Node.js Debugging:** `node --inspect app.js` se Chrome DevTools ko Node.js process se connect kiya ja sakta hai.

**Breakpoints:** Specific lines pe execution ko pause karna, taaki us moment ki variable-values inspect ki ja sakein.

## 5. Syntax

```js
debugger; // Programmatically triggers a breakpoint (when DevTools is open)

console.table(data);
console.group("Label");
console.trace();
console.assert(condition, message);
```

## 6. Basic Examples

```js
function calculateTotal(items) {
  debugger; // Execution pauses here when DevTools is open
  return items.reduce((sum, item) => sum + item.price, 0);
}
```

## 7. Detailed Examples

**Beyond `console.log()` — the underused console methods:**
```js
const users = [
  { id: 1, name: "Rohan", age: 28 },
  { id: 2, name: "Divya", age: 24 }
];

console.table(users); // Renders a beautiful, readable TABLE in the console — way better than console.log!

console.group("User Processing");
console.log("Starting processing...");
console.log("Processing user 1...");
console.groupEnd(); // Groups related logs together, collapsible in DevTools

console.time("operation"); // Start a timer
// ... some operation ...
console.timeEnd("operation"); // "operation: 45.231ms" — measure performance directly!

console.assert(users.length > 0, "Users array should not be empty!"); // Only logs if the condition is FALSE

console.trace("Reached this point"); // Prints a full stack trace — useful for tracing HOW code got here
```

**Setting breakpoints in browser DevTools — the workflow:**
```
1. Open DevTools (F12) → Sources tab
2. Find your JavaScript file
3. Click on a line number to set a breakpoint (a red marker appears)
4. Trigger the code (e.g., click a button that runs the function)
5. Execution PAUSES at that line — you can now:
   - Hover over variables to see their current values
   - Use the "Scope" panel to see all local/closure variables
   - Step Over (next line), Step Into (enter a function call), Step Out (finish current function)
   - Type expressions in the Console while paused to test things
```

**Reading a stack trace systematically (revisiting Module 08, chapter 03):**
```js
function processOrder(order) {
  validateOrder(order); // Line 2
}
function validateOrder(order) {
  checkInventory(order.items); // Line 5
}
function checkInventory(items) {
  throw new Error("Item out of stock: " + items[0]); // Line 8
}

processOrder({ items: ["Laptop"] });

// Stack trace (read TOP to BOTTOM):
// Error: Item out of stock: Laptop
//     at checkInventory (app.js:8)     <- WHERE the error was thrown
//     at validateOrder (app.js:5)       <- who called checkInventory
//     at processOrder (app.js:2)         <- who called validateOrder
//     at Object.<anonymous> (app.js:11)   <- the original call
```
**Debugging strategy:** Start from the **top** (where error actually occurred), then trace **downward** through the call-chain to understand the full context of how the code reached that failing state.

**Conditional breakpoints — for loops/repeated calls:**
```js
function processItems(items) {
  items.forEach((item, index) => {
    // In DevTools, you can set a CONDITIONAL breakpoint here: "index === 5"
    // This only pauses execution on the 6th iteration, not every single one!
    console.log(item);
  });
}
```

**A systematic debugging approach — the scientific method for bugs:**
```
1. REPRODUCE — Can you reliably make the bug happen?
2. ISOLATE — Narrow down WHERE the problem is (binary search through the code, if needed)
3. HYPOTHESIZE — What do you THINK is causing it?
4. TEST — Add a breakpoint/log to verify your hypothesis
5. FIX — Make the change
6. VERIFY — Confirm the bug is actually gone, and nothing else broke
```

## 8. Mental Model

Debugging ko socho ek **detective investigation** — `console.log` "witness statements" collect karna hai (helpful but limited), breakpoints "crime scene ko freeze karke har detail inspect karna" hai (much more powerful), aur stack trace "events ka exact timeline" hai jo batata hai kya-kab hua.

## 9. What Happens Internally?

Jab breakpoint hit hota hai, JavaScript engine execution ko **pause** kar deta hai us exact point pe — current Execution Context (Module 08, chapter 03 se yaad karo) ke saare variables, scope-chain, aur call-stack **poori tarah inspectable** ban jaate hain DevTools ke through, jaise ek "frozen frame" jise tum har angle se dekh sakte ho.

## 10. Common Mistakes

- Sirf `console.log` pe depend karna jab breakpoints/debugger bahut zyada powerful information de sakte hain.
- Stack trace ko bottom-se-top padhna (galat direction) — top se shuru karna chahiye.
- Debugging ke time systematic approach na follow karna, random changes karte rehna "shayad ye fix ho jaaye" wale mindset se.

## 11. Edge Cases

Async code debug karna trickier hota hai — breakpoints ko `await`/`.then()` callbacks ke andar bhi lagana padta hai, aur "async stack traces" (jo modern DevTools support karte hain) full context dikhane mein help karte hain jo purane traces nahi dikha pate the (Module 15 ke concepts yaha directly relevant hain).

## 12. Real-World Usage

Professional developers apna zyadatar debugging time DevTools/debugger use karke bitate hain, sirf `console.log` pe rarely depend karte hain complex bugs ke liye. Production applications mein error-tracking tools (Sentry, LogRocket) automatically stack traces capture karke developers ko alert karte hain.

## 13. Comparison With Related Concepts

| Technique | Best For |
|-----------|--------------|
| `console.log` | Quick, simple checks |
| `console.table`/`group`/`time` | Structured, readable debugging output |
| Breakpoints (debugger) | Deep inspection, step-by-step execution |
| Stack traces | Understanding error origin and call-chain |

## 14. Practice Questions

1. Stack trace ko kis direction mein padhna chahiye (top-to-bottom ya bottom-to-top)?
2. Breakpoint aur `console.log` mein kya practical difference hai debugging power ke terms mein?
3. Conditional breakpoint kis situation mein useful hai?

## 15. Challenge

Ek function likho jisme intentionally ek bug ho (jaise off-by-one error ek loop mein). Browser console mein `debugger;` statement use karke bug ko isolate karo, variable values inspect karke root-cause identify karo.

## 16. Interview Questions

**Q1: Breakpoints `console.log` se debugging ke liye kaise better hote hain?**
A: `console.log` sirf **predetermined points** pe **predetermined information** dikhata hai — tumhe pehle se pata hona chahiye kya print karna hai, aur har naye check ke liye code modify karna padta hai. Breakpoints execution ko **completely pause** kar dete hain ek specific line pe, jisse tum us moment ki **saari** variables, scope-chain, aur call-stack ko interactively inspect kar sakte ho — bina code modify kiye, real-time mein expressions test kar sakte ho, aur step-by-step (line-by-line) execution ko trace kar sakte ho. Ye complex bugs ke liye significantly more powerful aur efficient hai.

**Q2: Ek stack trace padhte waqt kya approach followकरनी chahiye root-cause dhundne ke liye?**
A: Stack trace ko **top se bottom** padhna chahiye. **Sabse upar** wo exact line/function hai jaha error actually **throw** hua — ye starting point hai investigation ka. Uske neeche wali entries dikhati hain **kisne kisko call kiya** (calling chain), jo context provide karti hain ki application us failing state tak kaise pahunchi. Ye "reverse chronological" call-order hai — sabse recent call (jaha crash hua) top pe, sabse purana call (jaha se sab shuru hua) bottom pe.

## 17. Chapter Summary

Debugging tools (browser DevTools, Node.js `--inspect`) `console.log` se kahi zyada powerful hain — breakpoints se execution pause karke deep-inspect kiya ja sakta hai. `console` object ke advanced methods (`table`, `group`, `time`, `trace`) structured debugging output dete hain. Stack traces top-se-bottom padhe jaate hain — error-origin se lekar call-chain tak.

## 18. Revision Checklist

- [ ] Breakpoints set/use karna aata hai (conceptually, ya actually try kiya).
- [ ] `console` ke advanced methods (table, group, time) yaad hain.
- [ ] Stack trace padhne ka correct direction/approach clear hai.

---

**Module 21 Complete!** Next Module: [22-Performance/01-Big-O-Basics.md](../22-Performance/01-Big-O-Basics.md)
