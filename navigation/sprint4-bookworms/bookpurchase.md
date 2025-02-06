---
layout: page
title: Book Store
permalink: /bookstore/
---

<style> 
  .cart-container {
    max-width: 600px;
    margin: 20px auto;
    padding: 20px;
    background-color: #E8C4A4;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  .cart-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    background-color: #A57F5A;
    border: 1px solid #ddd;
    border-radius: 4px;
    margin-bottom: 10px;
  }
  .cart-buttons, .cart-inputs {
    text-align: center;
    margin-top: 20px;
  }
  .btn {
    background-color: #007bff;
    color: #fff;
    padding: 10px 15px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    margin: 5px;
  }
  .btn.clear {
    background-color: #dc3545;
  }
  .input-field {
    display: block;
    margin: 10px auto;
    width: 80%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
</style>

<div class="cart-container">
  <h2>Your Cart</h2>
  <div id="cartItems">
    <!-- Cart items will be dynamically added here -->
  </div>

  <div class="cart-inputs">
    <h3>Add a Book to Cart</h3>
    <input type="number" id="bookId" class="input-field" placeholder="Enter Book ID" />
    <input type="text" id="bookTitle" class="input-field" placeholder="Enter Book Title" />
    <div>
      <input
        type="text"
        id="bookPrice"
        class="input-field"
        placeholder="Price will be generated"
        readonly
      />
      <button id="generatePriceBtn" class="btn">Generate Price</button>
    </div>
    <input type="number" id="bookQuantity" class="input-field" placeholder="Enter Quantity" />
    <input type="text" id="username" class="input-field" placeholder="Enter Your Username" />
  </div>

  <div class="cart-buttons">
    <button id="addToCartBtn" class="btn">Add Book to Cart</button>
    <button id="clearCartBtn" class="btn clear">Clear Cart</button>
  </div>
</div>

<script type="module">
  import { pythonURI, fetchOptions } from "{{site.baseurl}}/assets/js/api/config.js";

  document.addEventListener("DOMContentLoaded", function () {
    // Fetch cart items on page load
    fetchCartItems();

    // Attach event listeners to buttons
    document.getElementById("generatePriceBtn").addEventListener("click", generatePrice);
    document.getElementById("addToCartBtn").addEventListener("click", addToCart);
    document.getElementById("clearCartBtn").addEventListener("click", clearCart);
  });

  // Fetch and display cart items
function fetchCartItems() {
  fetch(`${pythonURI}/api/cart`)
    .then(response => response.json())
    .then(data => {
      const cartItemsContainer = document.getElementById("cartItems");
      cartItemsContainer.innerHTML = ""; // Clear current items

      if (data.items && data.items.length > 0) {
        data.items.forEach(item => {
          const cartItemDiv = document.createElement("div");
          cartItemDiv.classList.add("cart-item");
          cartItemDiv.innerHTML = `
            <span>${item.title}</span> <!-- Removed author reference -->
            <span>Price: $${item.price} | Quantity: ${item.quantity}</span>
          `;
          cartItemsContainer.appendChild(cartItemDiv);
        });
      } else {
        cartItemsContainer.innerHTML = "<p>Your cart is empty.</p>";
      }
    })
    .catch(error => {
      console.error("Error fetching cart items:", error);
    });
}

  // Generate a random price between 1 and 15
  function generatePrice() {
    const randomPrice = (Math.random() * 14 + 1).toFixed(2);
    document.getElementById("bookPrice").value = randomPrice;
  }

  // Add a book to the cart
  function addToCart() {
    const bookId = document.getElementById("bookId").value.trim();
    const bookTitle = document.getElementById("bookTitle").value.trim();
    const bookPrice = document.getElementById("bookPrice").value.trim();
    const bookQuantity = document.getElementById("bookQuantity").value.trim();
    const username = document.getElementById("username").value.trim();

    if (bookId && bookTitle && bookPrice && bookQuantity && username) {
      const data = {
        id: parseInt(bookId),
        title: bookTitle,
        price: parseFloat(bookPrice),
        quantity: parseInt(bookQuantity),
        _name: username
      };

      fetch(`${pythonURI}/api/cart`, {
        ...fetchOptions,
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data)
      })
        .then(response => response.json())
        .then(data => {
          alert(data.message || "Book added to cart!");
          fetchCartItems();
        })
        .catch(error => {
          console.error("Error adding book to cart:", error);
        });
    } else {
      alert("Please fill out all fields before adding the book to the cart.");
    }
  }

  // Clear the cart
  function clearCart() {
    const username = document.getElementById("username").value.trim();
    if (!username) {
      alert("Please enter your username before clearing the cart.");
      return;
    }

    fetch(`${pythonURI}/api/cart`, {
      ...fetchOptions,
      method: "DELETE",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ username })
    })
      .then(response => response.json())
      .then(data => {
        alert(data.message || "Cart cleared successfully!");
        fetchCartItems();
      })
      .catch(error => {
        console.error("Error clearing cart:", error);
      });
  }
</script>
