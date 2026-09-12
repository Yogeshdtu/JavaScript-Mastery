# Project: Shopping Cart

**Difficulty:** 🟡 Intermediate
**Modules Needed:** 06-07, 09
**Previous:** [04-Movie-Search-Application](../04-Movie-Search-Application/README.md)
**Next:** [06-Dashboard](../06-Dashboard/README.md)

---

## 1. Requirements

Ek product-listing page jisme items "cart" mein add ho sakein, quantities adjust ho sakein, aur total price calculate ho.

## 2. Features

- Product grid (from a hardcoded array)
- Add to cart / remove from cart
- Quantity increment/decrement
- Cart total with tax calculation
- Empty-cart state

## 3. Architecture

```
shopping-cart/
├── index.html
├── style.css
└── script.js
```

## 4. Step-by-Step Build Process

**Step 1 — Product data (Module 06, chapter 01; Module 07, chapter 01):**
```js
const products = [
  { id: 1, name: "Wireless Mouse", price: 799 },
  { id: 2, name: "Mechanical Keyboard", price: 3499 },
  { id: 3, name: "USB-C Hub", price: 1299 },
  { id: 4, name: "Laptop Stand", price: 1899 }
];

let cart = []; // [{ productId, quantity }]
const TAX_RATE = 0.18;
```

**Step 2 — Cart logic (Module 06's array methods heavily applied):**
```js
function addToCart(productId) {
  const existingItem = cart.find(item => item.productId === productId); // Module 06, ch 03!
  if (existingItem) {
    existingItem.quantity++;
  } else {
    cart.push({ productId, quantity: 1 });
  }
  renderCart();
}

function updateQuantity(productId, delta) {
  const item = cart.find(item => item.productId === productId);
  if (!item) return;

  item.quantity += delta;
  if (item.quantity <= 0) {
    cart = cart.filter(i => i.productId !== productId); // Remove if quantity drops to 0
  }
  renderCart();
}

function removeFromCart(productId) {
  cart = cart.filter(item => item.productId !== productId);
  renderCart();
}

function calculateSubtotal() {
  return cart.reduce((total, item) => { // Module 06, chapter 03's reduce again!
    const product = products.find(p => p.id === item.productId);
    return total + (product.price * item.quantity);
  }, 0);
}

function calculateTotal() {
  const subtotal = calculateSubtotal();
  return subtotal + (subtotal * TAX_RATE);
}
```

**Step 3 — Rendering (Module 11's DOM manipulation, Module 06's map):**
```js
function renderProducts() {
  const grid = document.getElementById("productGrid");
  grid.innerHTML = products.map(product => `
    <div class="product-card">
      <h3>${product.name}</h3>
      <p>₹${product.price}</p>
      <button class="add-btn" data-id="${product.id}">Add to Cart</button>
    </div>
  `).join("");
}

function renderCart() {
  const cartContainer = document.getElementById("cartItems");

  if (cart.length === 0) {
    cartContainer.innerHTML = "<p>Your cart is empty</p>";
  } else {
    cartContainer.innerHTML = cart.map(item => {
      const product = products.find(p => p.id === item.productId);
      return `
        <div class="cart-item" data-id="${item.productId}">
          <span>${product.name} x ${item.quantity}</span>
          <span>₹${(product.price * item.quantity).toFixed(2)}</span>
          <button class="qty-btn" data-action="decrease">-</button>
          <button class="qty-btn" data-action="increase">+</button>
          <button class="remove-btn">✗</button>
        </div>
      `;
    }).join("");
  }

  const subtotal = calculateSubtotal();
  document.getElementById("subtotal").textContent = `₹${subtotal.toFixed(2)}`;
  document.getElementById("tax").textContent = `₹${(subtotal * TAX_RATE).toFixed(2)}`;
  document.getElementById("total").textContent = `₹${calculateTotal().toFixed(2)}`;
}

document.getElementById("productGrid").addEventListener("click", (event) => {
  if (event.target.classList.contains("add-btn")) {
    addToCart(Number(event.target.dataset.id));
  }
});

document.getElementById("cartItems").addEventListener("click", (event) => {
  const cartItem = event.target.closest(".cart-item");
  if (!cartItem) return;
  const productId = Number(cartItem.dataset.id);

  if (event.target.dataset.action === "increase") updateQuantity(productId, 1);
  else if (event.target.dataset.action === "decrease") updateQuantity(productId, -1);
  else if (event.target.classList.contains("remove-btn")) removeFromCart(productId);
});

renderProducts();
renderCart();
```

## 5. Explanation

- Cart-state stores only `{ productId, quantity }` — **not** full product-copies. Product-details are looked-up on-demand via `find()`. This avoids data-duplication/sync-issues (a "single source of truth" principle, related to Module 26, chapter 01's DRY).
- `reduce()` (Module 06, chapter 03) calculates the subtotal by combining cart-quantities with product-prices — a classic "join two data-sets and aggregate" pattern.
- Event delegation with `data-action` attributes distinguishes multiple button-types within the same delegated listener.

## 6. Debugging

- Agar total galat aaye: `console.log(cart)` aur `console.log(products)` dono check karo — verify `productId`s match kar rahe hain (type-matching: numbers vs strings, Module 09).
- Agar quantity-buttons kaam na karein: verify `event.target.closest(".cart-item")` sahi element dhund raha hai (buttons cart-item ke andar nested hain).

## 7. Testing

Add multiple different products, increase/decrease quantities, verify totals recalculate correctly, remove all items and verify "empty cart" message shows.

## 8. Improvements

- Persist cart in localStorage (Module 11, chapter 05) so it survives page-reloads.
- Add product-images and a "quantity in stock" limit.
- Add a discount-code input (with validation logic).

## 9. Advanced Version

Connect to a real backend (Module 19-20) with an actual checkout-flow — creating an order in a database, simulating payment-processing, and showing an order-confirmation.

---

**Next:** [06-Dashboard](../06-Dashboard/README.md)
