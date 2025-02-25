---
layout: page
title: Book Store
permalink: /bookstore/
---

<style>
    .book-tile { margin: 10px; padding: 10px; border: 1px solid #ddd; display: inline-block; text-align: center; }
    .book-cover { width: 100px; height: auto; }
    .quantity-controls { margin-top: 5px; }
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
</style>

<div id="book-container"></div>
<h2>Shopping Cart</h2>
<div id="cartItems"></div>
<button id="clearCartButton">Clear Cart</button>

<script type="module">
    import { pythonURI, fetchOptions } from "{{site.baseurl}}/assets/js/api/config.js";

    document.addEventListener("DOMContentLoaded", function () {
        fetchBooks();
        fetchCartItems();
        document.getElementById("clearCartButton").addEventListener("click", clearCart);
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
                            <button class="add-to-cart" data-title="${book.title}" data-price="${price}">Add to Cart</button>
                        </div>
                    `;
                }).join('');
                document.querySelectorAll(".add-to-cart").forEach(button => {
                    button.addEventListener("click", function() {
                        addToCart(this.dataset.title, this.dataset.price);
                    });
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
                            <button class="delete-item" data-id="${item.id}">Remove</button>
                        `;
                        cartItemsContainer.appendChild(cartItemDiv);
                    });
                    document.querySelectorAll(".delete-item").forEach(button => {
                        button.addEventListener("click", function() {
                            deleteCartItem(this.dataset.id);
                        });
                    });
                } else {
                    cartItemsContainer.innerHTML = "<p>Your cart is empty.</p>";
                }
            })
            .catch(error => console.error("Error fetching cart items:", error));
    }

    window.updateQuantity = function(title, change) {
        const quantitySpan = document.getElementById(`quantity-${title}`);
        let currentQuantity = parseInt(quantitySpan.innerText);
        let newQuantity = Math.max(0, currentQuantity + change);
        quantitySpan.innerText = newQuantity;
    }

    window.addToCart = function(title, price) {
        price = parseFloat(price);
        const quantity = parseInt(document.getElementById(`quantity-${title}`).innerText);
        
        if (quantity > 0) {
            const data = { title, price, quantity };

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
            alert("Please select a quantity greater than zero before adding to the cart.");
        }
    }

    window.deleteCartItem = function(itemId) {
        fetch(`${pythonURI}/api/cart/${itemId}`, {
            ...fetchOptions,
            method: "DELETE",
        })
        .then(response => response.json())
        .then(data => {
            alert(data.message || "Item removed from cart.");
            fetchCartItems();
        })
        .catch(error => console.error("Error deleting cart item:", error));
    }

    window.clearCart = function() {
        fetch(`${pythonURI}/api/cart`, {
            ...fetchOptions,
            method: "DELETE",
        })
        .then(response => response.json())
        .then(data => {
            alert(data.message || "Cart cleared.");
            fetchCartItems();
        })
        .catch(error => console.error("Error clearing cart:", error));
    }
</script>
