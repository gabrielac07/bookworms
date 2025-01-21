---
layout: page
title: Book Store
permalink: /bookpurchase/
---
<div class="container my-5">
        <h1 class="text-center">Book Cart Management</h1>

<div id="cart-container" class="my-4">
            <h3>Cart Items</h3>
            <table class="table table-bordered">
                <thead>
                    <tr>
                        <th>Title</th>
                        <th>Price</th>
                        <th>Quantity</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody id="cart-items">
                    <!-- Items will be dynamically inserted here -->
                </tbody>
            </table>
            <div id="cart-summary" class="mt-3">
                <p><strong>Total Items:</strong> <span id="total-items">0</span></p>
                <p><strong>Total Price:</strong> $<span id="total-price">0.00</span></p>
                <button class="btn btn-danger" onclick="clearCart()">Clear Cart</button>
            </div>
        </div>

 <div id="add-item-form" class="my-4">
            <h3>Add a Book to the Cart</h3>
            <form onsubmit="addToCart(event)">
                <div class="mb-3">
                    <label for="title" class="form-label">Title</label>
                    <input type="text" class="form-control" id="title" required>
                </div>
                <div class="mb-3">
                    <label for="price" class="form-label">Price</label>
                    <input type="number" class="form-control" id="price" step="0.01" required>
                </div>
                <div class="mb-3">
                    <label for="quantity" class="form-label">Quantity</label>
                    <input type="number" class="form-control" id="quantity" required>
                </div>
                <div class="mb-3">
                    <label for="username" class="form-label">Username</label>
                    <input type="text" class="form-control" id="username" required>
                </div>
                <button type="submit" class="btn btn-primary">Add to Cart</button>
            </form>
        </div>
    </div>

   <script>
        const apiUrl = '/api/cart';

        // Fetch and display cart items
        async function fetchCart() {
            try {
                const response = await axios.get(apiUrl);
                const { items, total_items, total_price } = response.data;

                // Populate table with items
                const cartItems = document.getElementById('cart-items');
                cartItems.innerHTML = '';
                items.forEach(item => {
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td>${item.title}</td>
                        <td>$${item.price.toFixed(2)}</td>
                        <td>${item.quantity}</td>
                        <td>
                            <button class="btn btn-warning btn-sm" onclick="updateItem(${item.id})">Update</button>
                            <button class="btn btn-danger btn-sm" onclick="deleteItem(${item.id})">Remove</button>
                        </td>
                    `;
                    cartItems.appendChild(row);
                });

                // Update cart summary
                document.getElementById('total-items').textContent = total_items;
                document.getElementById('total-price').textContent = total_price.toFixed(2);
            } catch (error) {
                console.error('Error fetching cart:', error);
            }
        }

        // Add item to cart
        async function addToCart(event) {
            event.preventDefault();
            const title = document.getElementById('title').value;
            const price = parseFloat(document.getElementById('price').value);
            const quantity = parseInt(document.getElementById('quantity').value);
            const username = document.getElementById('username').value;

            try {
                await axios.post(apiUrl, { title, price, quantity, _name: username });
                alert('Item added successfully!');
                fetchCart();
            } catch (error) {
                console.error('Error adding to cart:', error);
                alert('Failed to add item to cart.');
            }
        }

        // Update item quantity
        async function updateItem(itemId) {
            const newQuantity = prompt('Enter new quantity:');
            if (!newQuantity || newQuantity <= 0) return;

            try {
                await axios.put(`${apiUrl}/${itemId}`, { quantity: parseInt(newQuantity) });
                alert('Item updated successfully!');
                fetchCart();
            } catch (error) {
                console.error('Error updating item:', error);
                alert('Failed to update item.');
            }
        }

        // Delete an item from the cart
        async function deleteItem(itemId) {
            try {
                await axios.delete(`${apiUrl}/${itemId}`);
                alert('Item removed successfully!');
                fetchCart();
            } catch (error) {
                console.error('Error removing item:', error);
                alert('Failed to remove item.');
            }
        }

        // Clear the entire cart
        async function clearCart() {
            if (!confirm('Are you sure you want to clear the cart?')) return;

            try {
                await axios.delete(apiUrl);
                alert('Cart cleared successfully!');
                fetchCart();
            } catch (error) {
                console.error('Error clearing cart:', error);
                alert('Failed to clear cart.');
            }
        }

        // Initial fetch
        fetchCart();
    </script>