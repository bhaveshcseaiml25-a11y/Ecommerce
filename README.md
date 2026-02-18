# Ecommerce
Java and HTML based e commerce website
This project is a web-based e-commerce application developed to provide an online shopping platform where users can browse products, add items to a cart, and place orders.

The application is designed using HTML, CSS, and JavaScript to create a responsive and interactive user interface. It works like a single-page application (SPA), meaning users can perform actions like adding products to the cart without reloading the page.

Code of the program:
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ShopHub | Mega Store</title>
<style>
/* Modern CSS Layers for Predictable Cascade */
@layer reset, base, components, utilities, theme;

/* Modern Reset */
@layer reset {
  *, *::before, *::after { box-sizing: border-box; }
  :root { -webkit-tap-highlight-color: transparent; }
  body { 
    min-height: 100vh; 
    line-height: 1.5; 
    -webkit-font-smoothing: antialiased;
    margin: 0;
    scroll-behavior: smooth;
  }
  img, picture, video, canvas, svg { 
    display: block; 
    max-width: 100%; 
    height: auto; 
  }
  input, button, select, textarea {
    font: inherit;
  }
}

/* CSS Custom Properties */
@layer base {
  :root {
    --primary: 229 55% 45%;
    --primary-50: 229 80% 98%;
    --primary-600: 229 55% 40%;
    --secondary: 195 80% 55%;
    --gray-50: 240 100% 98%;
    --gray-900: 240 10% 10%;
    --gray-950: 240 5% 3%;
    --radius: 1rem;
    --radius-sm: 0.5rem;
    --shadow-md: 0 10px 25px -5px rgb(0 0 0 / 0.1);
    --shadow-lg: 0 20px 40px -10px rgb(0 0 0 / 0.15);
    --space-md: 1.5rem;
    --space-lg: 2rem;
    --space-xl: 3rem;
  }
  
  .dark {
    --primary-50: 229 80% 15%;
    --gray-50: 240 5% 15%;
    --gray-900: 240 80% 98%;
    --gray-950: 240 100% 98%;
  }
}

@layer components {
  body {
    font-family: system-ui, -apple-system, sans-serif;
    background: hsl(var(--gray-50));
    color: hsl(var(--gray-950));
    transition: background 0.3s ease;
  }
  
  .btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0.75rem 1.5rem;
    border: none;
    border-radius: var(--radius-sm);
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
  }
  
  .btn-primary { background: hsl(var(--primary)); color: white; }
  .btn-primary:hover { background: hsl(var(--primary-600)); transform: translateY(-1px); }
  .btn-secondary { background: hsl(var(--secondary)); color: white; }
  
  header {
    position: sticky; top: 0; z-index: 100;
    background: hsl(var(--gray-900)); color: white;
    padding: 1rem 5%;
    display: flex; justify-content: space-between; align-items: center;
    box-shadow: var(--shadow-md);
  }

  .hero {
    background: linear-gradient(135deg, hsl(var(--primary)), hsl(var(--secondary)));
    color: white; padding: 4rem 1rem; text-align: center;
  }

  .search-input {
    width: 100%; max-width: 500px; padding: 1rem;
    border-radius: var(--radius-sm); border: none; margin-top: 1.5rem;
    box-shadow: var(--shadow-md);
  }

  .container { width: 90%; max-width: 1200px; margin: 3rem auto; }
  
  .category { margin-bottom: 4rem; }
  .category h2 { color: hsl(var(--primary)); border-bottom: 3px solid hsl(var(--secondary)); padding-bottom: 0.5rem; }

  .products {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 2rem;
    margin-top: 2rem;
  }

  .product {
    background: white; border-radius: var(--radius); padding: 1.5rem;
    box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); transition: 0.3s;
    display: flex; flex-direction: column;
  }

  .dark .product { background: hsl(var(--gray-900)); color: white; }

  .product:hover { transform: translateY(-5px); box-shadow: var(--shadow-lg); }

  .product img { width: 100%; aspect-ratio: 1; object-fit: cover; border-radius: 0.5rem; margin-bottom: 1rem; }

  /* Cart Styles */
  .cart-overlay {
    position: fixed; inset: 0; background: rgba(0,0,0,0.5); 
    visibility: hidden; opacity: 0; transition: 0.3s; z-index: 999;
  }
  .cart-overlay.open { visibility: visible; opacity: 1; }

  .cart-sidebar {
    position: fixed; right: 0; top: 0; height: 100%; width: 100%; max-width: 400px;
    background: hsl(var(--gray-900)); color: white; padding: 2rem;
    transform: translateX(100%); transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1);
    z-index: 1000; overflow-y: auto;
  }
  .cart-sidebar.open { transform: translateX(0); }

  .cart-item {
    display: flex; justify-content: space-between; align-items: center;
    background: rgba(255,255,255,0.1); padding: 1rem; border-radius: 0.5rem; margin-bottom: 1rem;
  }

  .checkout-section { display: none; padding: 4rem 1rem; }
  .checkout-section.active { display: block; }
}
</style>
</head>
<body>

<header>
  <h1>🛍️ ShopHub</h1>
  <div style="display: flex; gap: 1rem;">
    <button class="btn btn-secondary" onclick="toggleDarkMode()" aria-label="Toggle Dark Mode">🌓</button>
    <button class="btn btn-primary" onclick="toggleCart()" aria-label="View Cart">
      🛒 Cart (<span id="cart-count">0</span>)
    </button>
  </div>
</header>

<main id="main-content">
  <section class="hero" id="hero-section">
    <h2>Explore Our Full Catalog</h2>
    <p>Premium essentials delivered to your doorstep</p>
    <label for="search" class="sr-only">Search Products</label>
    <input type="text" id="search" class="search-input" placeholder="🔍 Search products or categories..." oninput="filterProducts()">
  </section>

  <div class="container" id="store-container">
    </div>

  <section class="checkout-section container" id="checkout-container">
    <button class="btn" onclick="showStore()" style="margin-bottom: 2rem;">← Back to Shopping</button>
    <div style="background: white; padding: 3rem; border-radius: var(--radius); color: black;">
      <h2>Secure Checkout</h2>
      <p>Total amount to pay: <strong>₹<span id="order-total">0</span></strong></p>
      <form onsubmit="placeOrder(event)" style="display: grid; gap: 1rem; margin-top: 2rem;">
        <input type="text" placeholder="Full Name" required class="btn" style="border: 1px solid #ccc; text-align: left;">
        <input type="email" placeholder="Email" required class="btn" style="border: 1px solid #ccc; text-align: left;">
        <button type="submit" class="btn btn-primary">Complete Purchase</button>
      </form>
    </div>
  </section>
</main>

<div class="cart-overlay" id="cart-overlay" onclick="toggleCart()"></div>
<aside class="cart-sidebar" id="cart-sidebar">
  <h2>Your Cart</h2>
  <div id="cart-items-list"></div>
  <div style="margin-top: 2rem; border-top: 1px solid #444; padding-top: 1rem;">
    <h3>Total: ₹<span id="cart-total-price">0</span></h3>
    <button class="btn btn-secondary" style="width: 100%; margin-top: 1rem;" onclick="goToCheckout()">Proceed to Checkout</button>
  </div>
</aside>

<script>
const products = [
  // --- ELECTRONICS ---
  { id: 1, name: 'Apple Watch Series 9', price: 41900, img: 'https://images.unsplash.com/photo-1523275335684-37898b6baf30?w=400', category: 'Electronics' },
  { id: 2, name: 'Sony WH-1000XM5', price: 29990, img: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=400', category: 'Electronics' },
  { id: 3, name: 'Keychron K8 Pro', price: 12999, img: 'https://images.unsplash.com/photo-1583394838336-acd977736f90?w=400', category: 'Electronics' },
  { id: 4, name: 'iPad Air M2', price: 59900, img: 'https://images.unsplash.com/photo-1544244015-0df4b3ffc6b0?w=400', category: 'Electronics' },
  { id: 7, name: 'Kindle Paperwhite', price: 14999, img: 'https://images.unsplash.com/photo-1592434134753-a70baf7979d7?w=400', category: 'Electronics' },
  { id: 8, name: 'GoPro HERO12', price: 38000, img: 'https://images.unsplash.com/photo-1565439380844-3d92026859f8?w=400', category: 'Electronics' },

  // --- ACCESSORIES ---
  { id: 5, name: 'Logitech G Pro Mouse', price: 15995, img: 'https://images.unsplash.com/photo-1610945262588-155d1b7a298e?w=400', category: 'Accessories' },
  { id: 6, name: 'JBL Flip 6', price: 11999, img: 'https://images.unsplash.com/photo-1620115314810-71dee5d89626?w=400', category: 'Accessories' },
  { id: 9, name: 'Anker PowerBank', price: 4500, img: 'https://images.unsplash.com/photo-1609091839311-d5365f9ff2c5?w=400', category: 'Accessories' },
  { id: 10, name: 'USB-C Hub Pro', price: 3200, img: 'https://images.unsplash.com/photo-1547119957-630f9c47b26d?w=400', category: 'Accessories' },
  { id: 11, name: 'Leather Desk Mat', price: 1800, img: 'https://images.unsplash.com/photo-1616627547584-bf28cee262db?w=400', category: 'Accessories' },

  // --- HOME & LIFESTYLE ---
  { id: 12, name: 'Smart Herb Garden', price: 8500, img: 'https://images.unsplash.com/photo-1585320806297-9794b3e4eeae?w=400', category: 'Home' },
  { id: 13, name: 'AeroPress Coffee Maker', price: 4200, img: 'https://images.unsplash.com/photo-1544667507-c7c1fa698d79?w=400', category: 'Home' },
  { id: 14, name: 'Modern Floor Lamp', price: 12500, img: 'https://images.unsplash.com/photo-1507473884658-c70b6559995f?w=400', category: 'Home' },
  { id: 15, name: 'Essential Oil Diffuser', price: 2900, img: 'https://images.unsplash.com/photo-1602928294221-441f1f274c97?w=400', category: 'Home' },
  { id: 16, name: 'Minimalist Wall Clock', price: 1500, img: 'https://images.unsplash.com/photo-1563861826100-9cb868fdbe1c?w=400', category: 'Home' },

  // --- FITNESS ---
  { id: 17, name: 'Premium Yoga Mat', price: 3500, img: 'https://images.unsplash.com/photo-1592419044706-39796d40f98c?w=400', category: 'Fitness' },
  { id: 18, name: 'Adjustable Dumbbells', price: 18000, img: 'https://images.unsplash.com/photo-1583454110551-21f2fa20019b?w=400', category: 'Fitness' },
  { id: 19, name: 'Smart Water Bottle', price: 2200, img: 'https://images.unsplash.com/photo-1602143393494-14226071715e?w=400', category: 'Fitness' },
  { id: 20, name: 'Resistance Bands Set', price: 1200, img: 'https://images.unsplash.com/photo-1598289431512-b97b0917a63a?w=400', category: 'Fitness' },

  // --- TRAVEL ---
  { id: 21, name: 'Hardshell Carry-On', price: 14500, img: 'https://images.unsplash.com/photo-1565026057447-bc90a3dceb87?w=400', category: 'Travel' },
  { id: 22, name: 'Memory Foam Pillow', price: 2100, img: 'https://images.unsplash.com/photo-1520206151081-9bf9c0474757?w=400', category: 'Travel' },
  { id: 23, name: 'Tech Organizer Pouch', price: 1800, img: 'https://images.unsplash.com/photo-1523170335258-f5ed11844a49?w=400', category: 'Travel' },
  { id: 24, name: 'Passport Wallet', price: 1500, img: 'https://images.unsplash.com/photo-1627123424574-724758594e93?w=400', category: 'Travel' }
];

let cart = JSON.parse(localStorage.getItem('shophub_cart')) || [];
let isDarkMode = localStorage.getItem('shophub_dark') === 'true';

function init() {
  renderStore();
  applyTheme();
  updateUI();
}

function renderStore() {
  const container = document.getElementById('store-container');
  const categories = [...new Set(products.map(p => p.category))];
  
  container.innerHTML = categories.map(cat => `
    <div class="category" data-cat="${cat.toLowerCase()}">
      <h2>${cat}</h2>
      <div class="products">
        ${products.filter(p => p.category === cat).map(p => `
          <div class="product" data-name="${p.name.toLowerCase()}">
            <img src="${p.img}" alt="${p.name}" loading="lazy">
            <h3>${p.name}</h3>
            <p style="font-size: 1.2rem; font-weight: bold; color: hsl(var(--primary))">₹${p.price.toLocaleString()}</p>
            <button class="btn btn-primary" onclick="addToCart(${p.id})">Add to Cart</button>
          </div>
        `).join('')}
      </div>
    </div>
  `).join('');
}

function filterProducts() {
  const query = document.getElementById('search').value.toLowerCase();
  document.querySelectorAll('.category').forEach(catSection => {
    let hasVisible = false;
    catSection.querySelectorAll('.product').forEach(prod => {
      const isMatch = prod.dataset.name.includes(query);
      prod.style.display = isMatch ? 'flex' : 'none';
      if (isMatch) hasVisible = true;
    });
    catSection.style.display = hasVisible ? 'block' : 'none';
  });
}

function addToCart(id) {
  const product = products.find(p => p.id === id);
  const inCart = cart.find(item => item.id === id);
  if (inCart) inCart.qty++;
  else cart.push({ ...product, qty: 1 });
  saveAndUpdate();
}

function updateQty(id, delta) {
  const item = cart.find(i => i.id === id);
  if (item) {
    item.qty += delta;
    if (item.qty <= 0) cart = cart.filter(i => i.id !== id);
  }
  saveAndUpdate();
}

function saveAndUpdate() {
  localStorage.setItem('shophub_cart', JSON.stringify(cart));
  updateUI();
}

function updateUI() {
  const list = document.getElementById('cart-items-list');
  const count = cart.reduce((acc, i) => acc + i.qty, 0);
  const total = cart.reduce((acc, i) => acc + (i.price * i.qty), 0);

  document.getElementById('cart-count').innerText = count;
  document.getElementById('cart-total-price').innerText = total.toLocaleString();
  document.getElementById('order-total').innerText = total.toLocaleString();

  list.innerHTML = cart.map(i => `
    <div class="cart-item">
      <span>${i.name} (x${i.qty})</span>
      <div style="display: flex; gap: 0.5rem; align-items:center;">
        <button class="btn" style="padding: 0.2rem 0.6rem; background: #555; color: white;" onclick="updateQty(${i.id}, -1)">-</button>
        <button class="btn" style="padding: 0.2rem 0.6rem; background: #555; color: white;" onclick="updateQty(${i.id}, 1)">+</button>
      </div>
    </div>
  `).join('') || '<p>Cart is empty</p>';
}

function toggleCart() {
  document.getElementById('cart-sidebar').classList.toggle('open');
  document.getElementById('cart-overlay').classList.toggle('open');
}

function goToCheckout() {
  if (cart.length === 0) return alert("Your cart is empty!");
  toggleCart();
  document.getElementById('store-container').style.display = 'none';
  document.getElementById('hero-section').style.display = 'none';
  document.getElementById('checkout-container').classList.add('active');
}

function showStore() {
  document.getElementById('store-container').style.display = 'block';
  document.getElementById('hero-section').style.display = 'block';
  document.getElementById('checkout-container').classList.remove('active');
}

function placeOrder(e) {
  e.preventDefault();
  alert("Order Placed Successfully!");
  cart = [];
  saveAndUpdate();
  showStore();
}

function toggleDarkMode() {
  isDarkMode = !isDarkMode;
  localStorage.setItem('shophub_dark', isDarkMode);
  applyTheme();
}

function applyTheme() {
  document.body.classList.toggle('dark', isDarkMode);
}

document.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
