---
layout: page
title: Book Store
permalink: /bookstore/
---

<style>
.book-tile {
    /*margin: 10px;
    padding: 10px;
    border: 1px solid #ddd;
    display: inline-block;
    text-align: center;
    width: 200px; /* Set a fixed width */
    /*height: 300px; /* Set a fixed height */
    display: inline-block;
    background-color: #E8C4A4;
    padding: 15px;
    border: 1px solid #ccc;
    border-radius: 8px;
    max-width: 200px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    text-align: center;
    color: #faebd8;
}

.book-cover {
    width: 190px; /* Ensure all images have the same width */
    height: 280px; /* Ensure all images have the same height */
    object-fit: cover; /* This ensures images are resized properly without distortion */
    border-radius: 5px; /* Optional: makes images look nicer */
}
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

.add-to-cart {
    display: block;
    margin: 20px auto;
    padding: 10px 20px;
    background-color: #a57e5a;
    color: white;
    border: 1px solid #a57e5a;
    border-radius: 4px;
    cursor: pointer;
}

.quality-buttons {
    display: inline-block;
    margin: 10px auto;
    padding: 5px 10px;
    background-color: #a57e5a;
    color: white;
    border: 1px solid #a57e5a;
    border-radius: 4px;
    cursor: pointer;
}

.add-to-cart:hover, .delete-item:hover, #clearCartButton:hover, .quality-buttons:hover {
    background-color: #500A0A;
    transition: 0.3s;
}

.delete-item, #clearCartButton {
    display: block;
    /*margin: 20px auto;*/
    padding: 5px 10px;
    background-color: #E8C4A4;
    color: white;
    border: 1px solid #E8C4A4;
    border-radius: 4px;
    cursor: pointer;
}

</style>

<h2>Shopping Cart</h2>
<div id="cartItems"></div>
<button id="clearCartButton">Clear Cart</button>
<h2>Check out some other great reads below!</h2>
<div id="book-container"></div>

<script type="module">
    import { pythonURI, fetchOptions } from "{{site.baseurl}}/assets/js/api/config.js";

    document.addEventListener("DOMContentLoaded", function () {
        fetchBooks();
        fetchCartItems();
        document.getElementById("clearCartButton").addEventListener("click", clearCart);
    });

    function fetchBooks() {
        fetch(`${pythonURI}/api/suggest/book`)
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
                                <button class="quality-buttons" onclick="updateQuantity('${book.title}', 1)">+</button>
                                <span id="quantity-${book.title}">0</span>
                                <button class="quality-buttons" onclick="updateQuantity('${book.title}', -1)">-</button>
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

    window.fetchCartItems = function() {
    fetch(`${pythonURI}/api/user`, {  
        method: "GET",
        credentials: "include"
    })
    .then(response => response.json())
    .then(user => {
        return fetch(`${pythonURI}/api/cart`, {
            ...fetchOptions,
            method: "GET",
            credentials: "include"
        });
    })
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
};


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
        fetch(`${pythonURI}/api/user`, {  // Fetch user credentials first
            method: "GET",
            credentials: "include"  // Ensure cookie-based auth works
        })
        .then(response => response.json())
        .then(user => {
            const data = {
                id: Date.now().toString(),  
                title,
                price,
                quantity,
                _name: user.uid  // ✅ Set _name to "uid" from credentials
            };

            return fetch(`${pythonURI}/api/cart`, {
                ...fetchOptions,
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(data),
                credentials: "include"
            });
        })
        .then(response => {
            if (!response.ok) {
                throw new Error(`HTTP error! Status: ${response.status}`);
            }
            return response.json();
        })
        .then(data => {
            alert(data.message || "Book added to cart!");
            fetchCartItems();
        })
        .catch(error => {
            console.error("Error adding book to cart:", error);
            alert("Failed to add book to cart. Please try again.");
        });
    } else {
        alert("Please select a quantity greater than zero before adding to the cart.");
    }
};



   window.deleteCartItem = function(itemId) {
    fetch(`${pythonURI}/api/user`, {  
        method: "GET",
        credentials: "include"
    })
    .then(response => response.json())
    .then(user => {
        return fetch(`${pythonURI}/api/cart/${itemId}`, {
            ...fetchOptions,
            method: "DELETE",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ _name: user.uid }), // ✅ Send user UID for token validation
            credentials: "include"
        });
    })
    .then(response => response.json())
    .then(data => {
        alert(data.message || "Item removed from cart.");
        fetchCartItems();
    })
    .catch(error => console.error("Error deleting cart item:", error));
};

window.clearCart = function() {
    fetch(`${pythonURI}/api/user`, {  
        method: "GET",
        credentials: "include"
    })
    .then(response => response.json())
    .then(user => {
        return fetch(`${pythonURI}/api/cart`, {
            ...fetchOptions,
            method: "DELETE",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ _name: user.uid }), // ✅ Send user UID for token validation
            credentials: "include"
        });
    })
    .then(response => response.json())
    .then(data => {
        alert(data.message || "Cart cleared.");
        fetchCartItems();
    })
    .catch(error => console.error("Error clearing cart:", error));
};

</script>
