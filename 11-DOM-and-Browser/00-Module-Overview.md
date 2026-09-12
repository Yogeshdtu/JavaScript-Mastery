# Module 11 — DOM and Browser

**Difficulty:** 🟡 Intermediate
**Chapters:** 6
**Estimated time:** 6-9 hours
**Prerequisites:** [Module 07 (Objects)](../07-Objects/00-Module-Overview.md), basic HTML/CSS samajh

---

## Is Module Mein Kya Hai?

Ab tak tumhara code console mein chalta tha. **Ab wo actual webpage ko control karega.**

DOM (Document Object Model) webpage ka ek JavaScript representation hai — har HTML element ek object ban jaata hai jise tum select, badal, bana, ya delete kar sakte ho. Yahin se JavaScript "interactive websites" wali language banti hai.

Ye module sabse **satisfying** hai — kyunki tumhara code ka result turant screen pe dikhta hai.

---

## Chapters

| # | Chapter | Kya Seekhoge |
|---|---------|--------------|
| 01 | [DOM Basics](./01-DOM-Basics.md) | DOM tree kya hai, nodes vs elements |
| 02 | [Selecting & Creating Elements](./02-Selecting-and-Creating-Elements.md) | ✅ `querySelector`, `createElement`, append |
| 03 | [Modifying Elements, Attributes, Styles](./03-Modifying-Elements-Attributes-Styles.md) | `textContent`, `classList`, styles |
| 04 | [Forms and Validation](./04-Forms-and-Validation.md) | Input values padhna, validate karna, submit handle karna |
| 05 | [Storage and Cookies](./05-Storage-and-Cookies.md) | `localStorage`, `sessionStorage`, cookies |
| 06 | [Browser APIs](./06-Browser-APIs.md) | Observers, geolocation, clipboard, aur zyada |

---

## Is Module Ke Baad Tum

- [ ] Page ke kisi bhi element ko select aur modify kar paoge.
- [ ] Dynamically list/cards render kar paoge data se.
- [ ] Data ko browser mein save kar paoge (page refresh ke baad bhi bacha rahega).
- [ ] Forms se input le kar validate kar paoge.

---

## Yahan Log Sabse Zyada Confuse Hote Hain

- **`innerHTML` vs `textContent`** — user input ke saath `innerHTML` use karna ek **XSS security hole** hai. Text dikhane ke liye hamesha `textContent`. (Chapter 03, aur Module 23 mein security detail)
- **`querySelectorAll` array nahi deta** — wo NodeList deta hai. `.forEach` chal jaata hai, lekin `.map`/`.filter` nahi. Convert karo: `[...nodeList]`. (Chapter 02)
- **Script HTML se pehle chal gaya** → elements `null` milte hain. Script ko `</body>` se pehle rakho ya `defer` use karo. (Chapter 01)
- **`className =` poora overwrite kar deta hai** — ✅ `classList.add/remove/toggle` use karo. (Chapter 03)
- **`localStorage` sirf strings store karta hai** — objects ke liye `JSON.stringify()`/`JSON.parse()` chahiye. (Chapter 05)

---

## Practice

- **Projects:** [Digital Clock](../27-Projects/Beginner/05-Digital-Clock/README.md), [To-Do App](../27-Projects/Beginner/04-To-Do-App/README.md), [Form Validation App](../27-Projects/Intermediate/07-Form-Validation-Application/README.md)
- **Cheat sheet:** [30-Revision/Cheat-Sheets/11-DOM-and-Events.md](../30-Revision/Cheat-Sheets/11-DOM-and-Events.md)
- **Interview prep:** [29-Interview-Preparation/06-DOM-and-Browser.md](../29-Interview-Preparation/06-DOM-and-Browser.md)

---

**Shuru karo:** [01-DOM-Basics.md](./01-DOM-Basics.md)
**Agla Module:** [12-Events](../12-Events/00-Module-Overview.md)
