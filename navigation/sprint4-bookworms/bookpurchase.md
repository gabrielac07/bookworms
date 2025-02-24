---
layout: page
title: Book Store
permalink: /bookstore/
---

<style>
        .book-tile { margin: 10px; padding: 10px; border: 1px solid #ddd; display: inline-block; text-align: center; }
        .book-cover { width: 100px; height: auto; }
        .quantity-controls { margin-top: 5px; }
  </style>

<h1>Book Store</h1>
  <div id="book-container"></div>
  <h2>Shopping Cart</h2>
  <div id="cartItems"></div>

<script type="module">
        import { pythonURI } from "{{site.baseurl}}/assets/js/api/config.js";

        document.addEventListener("DOMContentLoaded", function () {
            fetchBooks();
            fetchCartItems();
        });

        function fetchBooks() {
            fetch(`${pythonURI}/api/wishlist/books`)
                .then(response => response.json())
                .then(books => {
                    const bookContainer = document.getElementById("book-container");
                    bookContainer.innerHTML = books.map(book => {
                        const price = (Math.random() * 10 + 5).toFixed(2);
                        return `
                            <div class="book-tile">
                                <img src="${book.cover_url}" alt="${book.title}" class="book-cover" />
                                <h3>${book.title}</h3>
                                <p>Price: $${price}</p>
                                <div class="quantity-controls">
                                    <button onclick="updateQuantity('${book.title}', -1)">-</button>
                                    <span id="quantity-${book.title}">0</span>
                                    <button onclick="updateQuantity('${book.title}', 1)">+</button>
                                </div>
                            </div>
                        `;
                    }).join('');
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
    </script>

  


  

