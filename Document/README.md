<!-- Cart Page Functionality For bBest Seller -->

Of course, bro! Let's break the JavaScript code step-by-step so you understand exactly how it works. Here's the code again for reference:

```js
document.addEventListener("DOMContentLoaded", function () {
  document.querySelectorAll(".quick-add-form").forEach((form) => {
    form.addEventListener("submit", function (e) {
      e.preventDefault(); // 1
      const variantId = form.getAttribute("data-variant-id"); // 2

      // 3
      fetch("/cart/add.js", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ id: variantId, quantity: 1 }),
      })
        .then((res) => res.json()) // 4
        .then((data) => {
          // 5
          fetch("/cart.js")
            .then((res) => res.json())
            .then((cart) => {
              const count = cart.item_count; // 6
              const cartCountEl = document.querySelector(".cart-count"); // 7
              if (cartCountEl) {
                cartCountEl.textContent = count; // 8
                cartCountEl.classList.add("cart-updated"); // (Optional) animate
              }
            });
        })
        .catch((error) => {
          console.error("Error adding to cart:", error); // 9
        });
    });
  });
});
```

---

### 🔍 Explanation Step-by-Step:

---

#### `document.addEventListener('DOMContentLoaded', function () { ... });`

This makes sure your script only runs **after** the whole HTML page is loaded.

---

### 1. `e.preventDefault();`

Stops the form from submitting in the usual way (which would reload the page). We want to handle it via **JavaScript/AJAX** instead.

---

### 2. `const variantId = form.getAttribute('data-variant-id');`

Gets the `variant ID` of the product from a custom attribute on the form.

> Shopify products can have multiple variants, and we need the variant's ID to add to the cart.

---

### 3. `fetch('/cart/add.js', { ... })`

This makes a **POST request** to Shopify's cart API and adds the selected product variant to the cart — without reloading the page.

- `id: variantId`: the ID of the variant we want to add.
- `quantity: 1`: we’re adding 1 item.

---

### 4. `.then(res => res.json())`

This converts the response from Shopify (which is in JSON format) into a usable JavaScript object.

---

### 5. `fetch('/cart.js')`

After the product is added, we fetch the **entire cart** to check how many items are in it now.

---

### 6. `const count = cart.item_count;`

From the `/cart.js` response, we get the total number of items in the cart.

---

### 7. `const cartCountEl = document.querySelector('.cart-count');`

This grabs the **HTML element** on your site (usually in your navbar) that displays the current cart item count.

> Make sure your header has something like:

```html
<span class="cart-count">0</span>
```

---

### 8. `cartCountEl.textContent = count;`

This updates the number inside the cart icon to show the latest cart count — instantly, without refreshing the page.

---

### 9. `.catch(error => { ... })`

This catches any errors (e.g. network issues) and logs them in the console so you can debug.

---

### ✅ Final Result:

- You click **QUICK ADD**.
- Product gets added to cart in the background.
- Cart icon updates instantly.
- User never leaves the page.

<!-- Value persit -->

## Cart Value Persist

You're right: the `<span class="cart-count">0</span>` only updates **dynamically** after a product is added using JavaScript, but **on page refresh**, it goes back to the original `0` unless it's loaded from the current cart.

---

### ✅ Here's how to **persist the cart count on page load**:

You need to add **this small JS snippet** at the bottom of your theme layout (or inside a script tag where this span exists):

```html
<script>
  document.addEventListener("DOMContentLoaded", function () {
    fetch("/cart.js")
      .then((res) => res.json())
      .then((cart) => {
        const count = cart.item_count;
        const cartCountEl = document.querySelector(".cart-count");
        if (cartCountEl) {
          cartCountEl.textContent = count;
        }
      });
  });
</script>
```

---

### 💡 What This Does:

- When the page loads, it fetches the current cart from `/cart.js`.
- It gets the `item_count` from the response.
- It updates `.cart-count` span with the **real number of items in the cart**, even on refresh.

---

### ✅ So now you’ll have:

- **Initial load:** shows the actual cart count.
- **Quick Add clicks:** update it dynamically.
- **Refresh or navigate away and back:** still correct.
