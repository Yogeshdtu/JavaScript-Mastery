# Environment Setup

**Module:** 01-JavaScript-Foundations
**Difficulty:** 🟢 Beginner
**Previous:** [01-What-is-JavaScript.md](./01-What-is-JavaScript.md)
**Next:** [03-How-JavaScript-Runs.md](./03-How-JavaScript-Runs.md)

---

## 1. Learning Objectives

- Apne computer pe JavaScript likhne aur run karne ka setup ready karna.
- Browser console, Node.js, aur code editor teeno use karna seekhna.

## 2. Prerequisites

[01-What-is-JavaScript.md](./01-What-is-JavaScript.md)

## 3. Concept in Simple Hinglish

JavaScript chalane ke liye tumhe kuch bhi heavy install karne ki zaroorat nahi — bas ek browser (jo already hai) aur ek code editor kaafi hai. Backend JavaScript (Node.js) ke liye ek extra install chahiye, jo hum Module 18 tak pehle nahi bhi karein to chalega — abhi ke liye browser console se shuru karte hain.

## 4. Technical Explanation

JavaScript run karne ke 3 common tareeke hain:

1. **Browser Console (DevTools):** Instant, koi setup nahi, quick experiments ke liye best.
2. **HTML file + `<script>` tag:** Real webpages ke liye, DOM ke saath kaam karne ke liye.
3. **Node.js runtime:** Browser ke bahar JavaScript run karne ke liye — backend, scripts, tools.

## 5. Syntax

**Browser console kholna:**
- Chrome/Edge: `F12` ya `Ctrl+Shift+J`
- Firefox: `F12` ya `Ctrl+Shift+K`

**HTML file mein JavaScript:**
```html
<!DOCTYPE html>
<html>
<head><title>My First JS</title></head>
<body>
  <script>
    console.log("Hello from HTML file!");
  </script>
</body>
</html>
```

**Node.js file run karna (terminal mein):**
```bash
node app.js
```

## 6. Basic Examples

Browser console mein type karo:
```js
let name = "Rahul";
console.log("Hello " + name);
```

## 7. Detailed Examples

**Step-by-step: HTML file se JavaScript run karna**

1. Ek folder banao, usme `index.html` file banao.
2. Ye code likho:
```html
<!DOCTYPE html>
<html>
<head><title>Practice</title></head>
<body>
  <h1>Check Console</h1>
  <script src="script.js"></script>
</body>
</html>
```
3. Same folder mein `script.js` banao:
```js
console.log("Script file connected successfully!");
document.querySelector("h1").style.color = "blue";
```
4. `index.html` ko browser mein double-click karke kholo, phir `F12` se console check karo.

Isse tumne dekha JavaScript ko `<script src="">` se separately link karna best practice hai — inline `<script>` block se better, kyunki code organize rehta hai.

## 8. Mental Model

Socho HTML "skeleton/structure" hai, CSS "makeup/styling" hai, aur JavaScript "brain/behavior" hai. Teeno alag files mein rakhna (separation of concerns) professional practice hai.

## 9. What Happens Internally?

Jab browser `index.html` load karta hai:
1. HTML parse hota hai top se bottom.
2. Jab `<script>` tag milta hai, browser (by default) HTML parsing **rok deta hai**, script download/execute karta hai, phir aage badhta hai.
3. Isi liye `<script>` tag ko usually `</body>` ke just before rakha jaata hai — taaki HTML pehle load ho jaaye.

## 10. Common Mistakes

- Script tag ko `<head>` mein rakhna bina `defer`/`async` ke — isse page render slow ho jaata hai.
- File save na karna aur wonder karna "code change kyun nahi dikh raha" — browser refresh (`Ctrl+R`) yaad rakho.
- Console mein directly complex multi-line code paste karna — kabhi-kabhi browser security paste block karta hai; simple examples ke liye theek hai.

## 11. Edge Cases

- Console output browser restart pe clear ho jaata hai — console history persistent nahi hai (jab tak explicitly preserve log on navigation on na karo).
- Kuch corporate/managed browsers mein DevTools disabled ho sakta hai — tab local HTML file approach use karo.

## 12. Real-World Usage

Professional developers apna code VS Code jaise editor mein likhte hain, terminal se run/test karte hain, aur browser DevTools ka use debugging ke liye karte hain — ye teen tools daily workflow ka core hain.

## 13. Comparison With Related Concepts

| Method | Best For |
|--------|----------|
| Browser Console | Quick testing, experiments |
| HTML + script tag | Frontend/DOM projects |
| Node.js | Backend, CLI tools, scripts |

## 14. Practice Questions

1. Apne browser mein console kholo aur `2 ** 10` ka result nikaalo.
2. Ek HTML file banao jisme JavaScript se `alert("Hello")` chale.

## 15. Challenge

Ek HTML file banao jisme JavaScript se page ka background color har button click pe random color mein change ho (abhi functions/events nahi padhe, to sirf `document.body.style.background = "red"` type simple line try karo).

## 16. Interview Questions

**Q1: `<script>` tag ko body ke end mein kyun rakha jaata hai?**
A: Kyunki script tag ko parse/execute karte waqt browser HTML parsing pause kar deta hai. Agar script `<head>` mein ho bina `defer`/`async` attribute ke, to page ka content render hone mein delay hota hai. Body ke end mein rakhne se HTML pehle render ho jaata hai, phir JS chalta hai.

**Q2: `defer` aur `async` attributes ka kya use hai?**
A: Dono script ko HTML parsing block kiye bina download karne dete hain. `async` script download hote hi execute ho jaata hai (order guarantee nahi), jabki `defer` script HTML parsing complete hone ke baad, document order mein execute hota hai. Multiple scripts ke beech dependency ho to `defer` safer hai.

## 17. Chapter Summary

JavaScript run karne ke teen main tareeke hain — browser console (quick testing), HTML+script tag (frontend projects), aur Node.js (backend/scripts). Script tag ki placement aur `defer`/`async` attributes page performance ko affect karte hain.

## 18. Revision Checklist

- [ ] Browser console khol ke code run kar sakta hoon.
- [ ] HTML file mein external JS file link kar sakta hoon.
- [ ] `defer` vs `async` ka difference samajh gaya.

---

**Next:** [03-How-JavaScript-Runs.md](./03-How-JavaScript-Runs.md)
