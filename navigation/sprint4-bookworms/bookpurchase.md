---
layout: page
title: Book Depository
permalink: /bookpurchase/
---
<style>
    .App {
  font-family: Arial, sans-serif;
  text-align: center;
  margin: 20px;
        }

    .book-list {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
        }

    .book-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  width: 200px;
  text-align: left;
        }

    .book-card h3 {
  margin: 0 0 10px;
        }

    .quantity-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px 0;
        }

    .quantity-controls button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 4px;
        }

    .quantity-controls button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
        }

    .quantity-controls span {
  margin: 0 10px;
  font-size: 16px;
        }

    button {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 5px;
  cursor: pointer;
        }

    button:hover {
  background-color: #218838;
        }

<header>
    <h1>Library Bookstore</h1>
  </header>
  <h2>Books Available</h2>
  <div id="book-list" class="book-list"></div>

  <script>
    // Fetch available books and render them
    const bookList = document.getElementById('book-list');
    const cart = {};

    // Fetch books from API
    fetch('http://172.19.255.157:8887/api/random_book')
      .then(response => response.json())
      .then(data => {
        const books = data.books;
        books.forEach(book => {
          const bookCard = document.createElement('div');
          bookCard.classList.add('book-card');
          bookCard.innerHTML = `
            <h3>${book.title}</h3>
            <p>Price: $${book.price.toFixed(2)}</p>
            <div class="quantity-controls">
              <button onclick="updateQuantity(${book.id}, -1)" id="minus-${book.id}" disabled>-</button>
              <span id="quantity-${book.id}">1</span>
              <button onclick="updateQuantity(${book.id}, 1)">+</button>
            </div>
            <button onclick="addToCart(${book.id}, '${book.title}', ${book.price})">Add to Cart</button>
          `;
          bookList.appendChild(bookCard);
        });
      })
      .catch(error => console.error('Error fetching books:', error));

    // Update quantity
    function updateQuantity(bookId, change) {
      const quantitySpan = document.getElementById(`quantity-${bookId}`);
      let quantity = parseInt(quantitySpan.innerText);
      quantity = Math.max(1, quantity + change);
      quantitySpan.innerText = quantity;

      // Enable or disable minus button
      const minusButton = document.getElementById(`minus-${bookId}`);
      minusButton.disabled = quantity <= 1;
      cart[bookId] = quantity;
    }

    // Add book to cart
    function addToCart(bookId, title, price) {
      const quantity = cart[bookId] || 1;
      fetch('http://127.0.0.1:8887/api/cart', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          title: title,
          price: price,
          quantity: quantity,
          username: 'CurrentUser', // Replace with authenticated username
        }),
      })
        .then(response => {
          if (response.ok) {
            alert(`${quantity} copy/copies of "${title}" added to the cart.`);
          } else {
            alert('Error adding to cart.');
          }
        })
        .catch(error => console.error('Error adding to cart:', error));
    }
  </script>