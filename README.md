# onlinefood
An Online Food Ordering System is a web and mobile app that lets customers browse restaurant menus, place orders, and pay online. It streamlines food delivery with real-time order tracking and automated kitchen management, boosting convenience for diners while helping restaurants increase sales and reduce manual order errors. Would yo
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QuickBite - Online Food Ordering</title>
  <style>
    * { box-sizing: border-box; font-family: system-ui, -apple-system, sans-serif; }
    body { margin: 0; background: #f4f6f8; color: #333; }
    header { background: #ff5200; color: white; padding: 1rem 2rem; display: flex; justify-content: space-between; align-items: center; }
    .container { display: grid; grid-template-columns: 2fr 1fr; gap: 2rem; max-width: 1100px; margin: 2rem auto; padding: 0 1rem; }
    .menu-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 1rem; }
    .card, .cart-box { background: white; padding: 1.2rem; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.08); }
    .card h3 { margin: 0 0 0.5rem; }
    .price { font-weight: bold; color: #2e7d32; margin-bottom: 1rem; }
    button { background: #ff5200; color: white; border: none; padding: 0.6rem 1rem; border-radius: 6px; cursor: pointer; font-weight: 600; width: 100%; }
    button:hover { background: #e04800; }
    .cart-item { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #eee; padding: 0.5rem 0; }
    .qty-btn { width: auto; padding: 0.2rem 0.6rem; margin: 0 0.2rem; background: #ddd; color: #333; }
    .promo-box { display: flex; gap: 0.5rem; margin: 1rem 0; }
    input { flex: 1; padding: 0.5rem; border: 1px solid #ccc; border-radius: 6px; }
    .status-banner { margin-top: 1rem; padding: 0.8rem; background: #e8f5e9; border-left: 4px solid #2e7d32; display: none; font-weight: 600; }
    @media (max-width: 768px) { .container { grid-template-columns: 1fr; } }
  </style>
</head>
<body>

<header>
  <h2>🍔 QuickBite Food Delivery</h2>
  <span id="cart-count">Cart: 0 items</span>
</header>

<div class="container">
  <!-- Menu Section -->
  <div>
    <h3>Explore Menu</h3>
    <div class="menu-grid" id="menu-list"></div>
  </div>

  <!-- Cart & Checkout Section -->
  <div class="cart-box">
    <h3>Your Cart</h3>
    <div id="cart-items"><p>Your cart is empty.</p></div>

    <div class="promo-box">
      <input type="text" id="promo-code" placeholder="Try code: SAVE20">
      <button style="width: auto;" onclick="applyPromo()">Apply</button>
    </div>

    <h4 id="total-price">Total: ₹0</h4>
    <button onclick="placeOrder()">Place Order</button>

    <div class="status-banner" id="order-status"></div>
  </div>
</div>

<script>
  const menuData = [
    { id: 1, name: "Margherita Pizza", price: 299, category: "Italian" },
    { id: 2, name: "Paneer Tikka Burger", price: 179, category: "Fast Food" },
    { id: 3, name: "Hyderabadi Veg Biryani", price: 249, category: "Main Course" },
    { id: 4, name: "Masala Dosa", price: 120, category: "South Indian" },
    { id: 5, name: "Cold Coffee", price: 99, category: "Beverages" },
    { id: 6, name: "Chocolate Brownie", price: 149, category: "Dessert" }
  ];

  let cart = [];
  let discount = 0;

  // Render Menu
  const menuList = document.getElementById("menu-list");
  menuData.forEach(item => {
    menuList.innerHTML += `
      <div class="card">
        <small style="color:#777;">${item.category}</small>
        <h3>${item.name}</h3>
        <div class="price">₹${item.price}</div>
        <button onclick="addToCart(${item.id})">Add to Cart</button>
      </div>
    `;
  });

  function addToCart(id) {
    const existing = cart.find(item => item.id === id);
    if (existing) {
      existing.qty++;
    } else {
      const product = menuData.find(item => item.id === id);
      cart.push({ ...product, qty: 1 });
    }
    updateCartUI();
  }

  function changeQty(id, delta) {
    const item = cart.find(i => i.id === id);
    if (!item) return;
    item.qty += delta;
    if (item.qty <= 0) cart = cart.filter(i => i.id !== id);
    updateCartUI();
  }

  function applyPromo() {
    const code = document.getElementById("promo-code").value.trim().toUpperCase();
    if (code === "SAVE20") {
      discount = 0.20;
      alert("20% Discount Applied!");
    } else {
      discount = 0;
      alert("Invalid coupon code.");
    }
    updateCartUI();
  }

  function updateCartUI() {
    const cartContainer = document.getElementById("cart-items");
    const countSpan = document.getElementById("cart-count");
    const totalEl = document.getElementById("total-price");

    if (cart.length === 0) {
      cartContainer.innerHTML = "<p>Your cart is empty.</p>";
      countSpan.innerText = "Cart: 0 items";
      totalEl.innerText = "Total: ₹0";
      return;
    }

    let subtotal = 0;
    let totalItems = 0;
    cartContainer.innerHTML = "";

    cart.forEach(item => {
      subtotal += item.price * item.qty;
      totalItems += item.qty;
      cartContainer.innerHTML += `
        <div class="cart-item">
          <div>
            <strong>${item.name}</strong><br>
            <small>₹${item.price} x ${item.qty}</small>
          </div>
          <div>
            <button class="qty-btn" onclick="changeQty(${item.id}, -1)">-</button>
            <button class="qty-btn" onclick="changeQty(${item.id}, 1)">+</button>
          </div>
        </div>
      `;
    });

    const finalTotal = Math.round(subtotal * (1 - discount));
    countSpan.innerText = `Cart: ${totalItems} item(s)`;
    totalEl.innerText = discount > 0 
      ? `Total: ₹${finalTotal} (20% off applied)` 
      : `Total: ₹${finalTotal}`;
  }

  function placeOrder() {
    if (cart.length === 0) return alert("Please add items to your cart first!");
    
    const statusBox = document.getElementById("order-status");
    statusBox.style.display = "block";
    statusBox.innerText = "Status: ✅ Order Placed! Sending to kitchen...";

    setTimeout(() => { statusBox.innerText = "Status: 👨‍🍳 Chef is preparing your meal..."; }, 3000);
    setTimeout(() => { statusBox.innerText = "Status: 🛵 Out for delivery!"; }, 6000);
    setTimeout(() => { statusBox.innerText = "Status: 🎉 Delivered! Enjoy your food."; }, 9000);

    cart = [];
    discount = 0;
    updateCartUI();
  }
</script>

</body>
</html>
