---
layout: page
title: Book Store
permalink: /bookpurchase/
---
<style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
        }
        header {
            background-color: #333;
            color: white;
            padding: 1rem 2rem;
            text-align: center;
        }
        .container {
            max-width: 900px;
            margin: 2rem auto;
            background: white;
            padding: 1rem;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .book {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin: 1rem 0;
            padding: 1rem;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: #fafafa;
        }
        .book img {
            height: 100px;
            margin-right: 1rem;
        }
        .book-details {
            flex: 1;
        }
        .book-title {
            font-size: 1.2rem;
            margin: 0;
        }
        .book-price {
            margin: 0.5rem 0;
            font-weight: bold;
            color: #555;
        }
        .cart-controls {
            display: flex;
            align-items: center;
        }
        .cart-controls button {
            background-color: #007bff;
            color: white;
            border: none;
            padding: 0.5rem 0.8rem;
            margin: 0 0.5rem;
            border-radius: 5px;
            cursor: pointer;
        }
        .cart-controls button:disabled {
            background-color: #ccc;
            cursor: not-allowed;
        }
        .quantity {
            font-weight: bold;
            margin: 0 0.5rem;
        }
    </style>

<header>
        <h1>Bookstore</h1>
    </header>
    <div class="container" id="book-list">
        <h2>Books</h2>
        <p>Loading books...</p>
    </div>

<script>
        // Random price generator
        function getRandomPrice() {
            return (Math.random() * 14 + 1).toFixed(2); // Random price between 1 and 15
        }

        // Fetch books and render them
        async function fetchBooks() {
            try {
                const response = await fetch('http://172.19.255.157:8887/api/random_book'); // Change to your backend endpoint
                const books = await response.json();

                const bookList = document.getElementById('book-list');
                bookList.innerHTML = ''; // Clear loading message

                books.forEach(book => {
                    const price = getRandomPrice(); // Generate a random price
                    const bookElement = document.createElement('div');
                    bookElement.className = 'book';

                    bookElement.innerHTML = `
                        <img src="${book.cover}" alt="${book.title}" />
                        <div class="book-details">
                            <h3 class="book-title">${book.title}</h3>
                            <p class="book-price">$${price}</p>
                        </div>
                        <div class="cart-controls">
                            <button onclick="decreaseQuantity('${book.id}')">-</button>
                            <span class="quantity" id="quantity-${book.id}">1</span>
                            <button onclick="increaseQuantity('${book.id}')">+</button>
                            <button onclick="addToCart('${book.id}', '${book.title}', '${price}', '1')">Add to Cart</button>
                        </div>
                    `;

                    bookList.appendChild(bookElement);
                });
            } catch (error) {
                console.error('Error fetching books:', error);
            }
        }

        // Increase quantity
        function increaseQuantity(bookId) {
            const quantityElement = document.getElementById(`quantity-${bookId}`);
            let quantity = parseInt(quantityElement.textContent);
            quantityElement.textContent = ++quantity;
        }

        // Decrease quantity
        function decreaseQuantity(bookId) {
            const quantityElement = document.getElementById(`quantity-${bookId}`);
            let quantity = parseInt(quantityElement.textContent);
            if (quantity > 1) {
                quantityElement.textContent = --quantity;
            }
        }

        // Add to Cart
        async function addToCart(bookId, title, price, quantity) {
            const quantityElement = document.getElementById(`quantity-${bookId}`);
            const updatedQuantity = parseInt(quantityElement.textContent);

            const data = {
                id: bookId,
                title: title,
                price: parseFloat(price),
                quantity: updatedQuantity,
                _name: 'Guest' // Hardcoded username, replace with dynamic value if needed
            };

            try {
                const response = await fetch('http://127.0.0.1:8887/api/cart', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(data)
                });

                if (response.ok) {
                    alert('Item added to cart!');
                } else {
                    const error = await response.json();
                    alert(`Error: ${error.message}`);
                }
            } catch (error) {
                console.error('Error adding to cart:', error);
            }
        }

        // Initialize
        fetchBooks();
    </script>