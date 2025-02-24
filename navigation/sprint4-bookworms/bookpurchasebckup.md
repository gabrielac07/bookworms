---
layout: page
title: Book Store
permalink: /books/
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
    <select id="bookDropdown" class="input-field">
      <option value="">Select a Book</option>
    </select>
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
  </div>

  <div class="cart-buttons">
    <button id="addToCartBtn" class="btn">Add Book to Cart</button>
    <button id="clearCartBtn" class="btn clear">Clear Cart</button>
  </div>
</div>

<script type="module">
  import { pythonURI, fetchOptions } from "{{site.baseurl}}/assets/js/api/config.js";

document.addEventListener("DOMContentLoaded", function () {
    fetchBooks();
    fetchCartItems();
    document.getElementById("generatePriceBtn").addEventListener("click", generatePrice);
    document.getElementById("addToCartBtn").addEventListener("click", addToCart);
    document.getElementById("clearCartBtn").addEventListener("click", clearCart);
});

function fetchBooks() {
    fetch(`${pythonURI}/api/suggest/book`)
        .then(response => response.json())
        .then(books => {
            const bookDropdown = document.getElementById("bookDropdown");
            const bookList = document.getElementById("book-list-content");

            bookDropdown.innerHTML = '<option value="">Select a Book</option>';
            bookList.innerHTML = books.length === 0 
                ? '<p style="color: #000000">No books added yet. Fill out the form above to start adding your favorite books!</p>' 
                : books.map(book => `
                    <div class="book">
                        <h3>${book.title}</h3>
                        <p><strong>Author:</strong> ${book.author}</p>
                        <p><strong>Genre:</strong> ${book.genre}</p>
                        <p><strong>Description:</strong> ${book.description}</p>
                        <img src="${book.cover_url}" alt="Cover image of ${book.title}">
                        <div class="book-options">
                            <button class="updateButton" data-title="${book.title}">Update</button>
                            <button class="deleteButton" data-title="${book.title}">Delete</button>
                        </div>
                    </div>
                `).join('');

            books.forEach(book => {
                const option = document.createElement("option");
                option.value = book.title;
                option.textContent = book.title;
                bookDropdown.appendChild(option);
            });

            document.querySelectorAll(".updateButton").forEach(button => {
                button.addEventListener("click", event => updateBook(event.target.dataset.title));
            });

            document.querySelectorAll(".deleteButton").forEach(button => {
                button.addEventListener("click", event => deleteBook(event.target.dataset.title));
            });
        })
        .catch(error => console.error("Error fetching books:", error));
}

function fetchCartItems() {
    fetch(`${pythonURI}/api/cart`)
        .then(response => response.json())
        .then(data => {
            const cartItemsContainer = document.getElementById("cartItems");
            cartItemsContainer.innerHTML = "";
            if (data.items && data.items.length > 0) {
                data.items.forEach(item => {
                    const cartItemDiv = document.createElement("div");
                    cartItemDiv.classList.add("cart-item");
                    cartItemDiv.innerHTML = `
                        <span>${item.title}</span>
                        <span>Price: $${item.price} | Quantity: ${item.quantity}</span>
                    `;
                    cartItemsContainer.appendChild(cartItemDiv);
                });
            } else {
                cartItemsContainer.innerHTML = "<p>Your cart is empty.</p>";
            }
        })
        .catch(error => console.error("Error fetching cart items:", error));
}

function generatePrice() {
    const randomPrice = (Math.random() * 10 + 5).toFixed(2); // Generates a price between $5-$15
    document.getElementById("bookPrice").value = randomPrice;
}

function addToCart() {
    const bookTitle = document.getElementById("bookDropdown").value;
    const bookPrice = document.getElementById("bookPrice").value.trim();
    const bookQuantity = document.getElementById("bookQuantity").value.trim();

    if (bookTitle && bookPrice && bookQuantity) {
        const data = {
            title: bookTitle,
            price: parseFloat(bookPrice),
            quantity: parseInt(bookQuantity)
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
            .catch(error => console.error("Error adding book to cart:", error));
    } else {
        alert("Please select a book and enter all details before adding to the cart.");
    }
}

function clearCart() {
    fetch(`${pythonURI}/api/cart`, {
        ...fetchOptions,
        method: "DELETE",
        headers: { "Content-Type": "application/json" }
    })
        .then(response => response.json())
        .then(data => {
            alert(data.message || "Cart cleared successfully!");
            fetchCartItems();
        })
        .catch(error => console.error("Error clearing cart:", error));
}
</script>