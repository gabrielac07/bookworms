---
layout: post
title: Profile Settings
permalink: /profile
menu: nav/home.html
search_exclude: true
show_reading_time: false
---
<div class="profile-container">
  <div class="card">
    <form>
      <!-- Profile Update Section -->
      <div>
        <label for="newUid">Enter New UID:</label>
        <input type="text" id="newUid" placeholder="New UID">
      </div>
      <div>
        <label for="newName">Enter New Name:</label>
        <input type="text" id="newName" placeholder="New Name">
      </div>
      <div>
        <label for="newPassword">Enter New Password:</label>
        <input type="text" id="newPassword" placeholder="New Password">
      </div>
      <br>
      <!-- Wishlist Dropdown Section -->
      <div>
        <label for="bookDropdown">Select and Add Book to Wishlist:</label>
        <div class="icon-container">
          <select id="bookDropdown">
            <!-- Options will be dynamically populated -->
          </select>
          <i class="fas fa-plus" onclick="addBookToWishlist()"></i>
        </div>
      </div>
      <!-- Wishlist Table -->
      <table>
        <thead>
          <tr>
            <th>Book Title</th>
            <th>Author</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody id="wishlistResult">
          <!-- Wishlist books will be dynamically populated -->
        </tbody>
      </table>
      <!-- Profile Picture Upload -->
      <label for="profilePicture" class="file-icon">
        Upload Profile Picture <i class="fas fa-upload"></i>
      </label>
      <input type="file" id="profilePicture" accept="image/*" onchange="saveProfilePicture()">
      <div class="image-container" id="profileImageBox">
        <!-- Profile picture will be displayed here -->
      </div>
      <!-- Profile Message -->
      <p id="profile-message" style="color: red;"></p>
    </form>
  </div>
</div>

<script type="module">
  import { pythonURI, fetchOptions } from '{{site.baseurl}}/assets/js/api/config.js';
  import { postUpdate } from "{{site.baseurl}}/assets/js/api/profile.js";

  let predefinedBooks = [];
  let userWishlist = [];
  const userId = 1; // Replace with dynamic user ID logic if needed

  // Fetch predefined books for the dropdown
  async function fetchPredefinedBooks() {
    const URL = `${pythonURI}/api/wishlist/books`; // Backend endpoint to fetch all books
    try {
      const response = await fetch(URL, fetchOptions);
      if (!response.ok) {
        throw new Error(`Failed to fetch predefined books: ${response.status}`);
      }
      const books = await response.json();
      console.log("Predefined books fetched:", books); // Debug log
      return books;
    } catch (error) {
      console.error('Error fetching predefined books:', error.message);
      return [];
    }
  }

  // Populate the book dropdown
  function populateBookDropdown(predefinedBooks) {
    const bookDropdown = document.getElementById('bookDropdown');
    bookDropdown.innerHTML = '';
    if (predefinedBooks.length === 0) {
      const placeholderOption = document.createElement('option');
      placeholderOption.textContent = 'No books available';
      placeholderOption.disabled = true;
      bookDropdown.appendChild(placeholderOption);
    } else {
      predefinedBooks.forEach(book => {
        const option = document.createElement('option');
        option.value = book.id; // Assuming `book` has an `id` property
        option.textContent = `${book.title} by ${book.author}`;
        bookDropdown.appendChild(option);
      });
    }
  }

  // Fetch user's wishlist
  async function fetchWishlist() {
    const URL = `${pythonURI}/api/wishlist/?user_id=${userId}`; // Backend endpoint for fetching user's wishlist
    try {
      const response = await fetch(URL, fetchOptions);
      if (!response.ok) {
        throw new Error(`Failed to fetch wishlist: ${response.status}`);
      }
      const wishlist = await response.json();
      console.log("Wishlist fetched:", wishlist); // Debug log
      return wishlist;
    } catch (error) {
      console.error('Error fetching wishlist:', error.message);
      return [];
    }
  }

  // Add a book to the wishlist
  window.addBookToWishlist = async function () {
    const dropdown = document.getElementById('bookDropdown');
    const selectedOption = dropdown.options[dropdown.selectedIndex];
    const bookId = selectedOption.value;

    if (!bookId) {
      document.getElementById('profile-message').textContent = 'Please select a book.';
      return;
    }

    document.getElementById('profile-message').textContent = '';

    const URL = `${pythonURI}/api/wishlist/`; // Backend endpoint to add book to wishlist
    const body = { user_id: userId, book_id: parseInt(bookId) }; 

    try {
      const response = await fetch(URL, {
        ...fetchOptions,
        method: 'POST',
        body: JSON.stringify(body),
      });

      if (!response.ok) {
        const errorData = await response.json();
        throw new Error(errorData.error || `Failed to add book to wishlist: ${response.status}`);
      }

      // Refresh wishlist after adding the book
      userWishlist = await fetchWishlist();
      displayWishlist();
      document.getElementById('profile-message').textContent = 'Book added successfully!';
    } catch (error) {
      console.error('Error adding book to wishlist:', error.message);
      document.getElementById('profile-message').textContent = `Error: ${error.message}`;
    }
  };

  // Display the wishlist
  function displayWishlist() {
    const tableBody = document.getElementById('wishlistResult');
    tableBody.innerHTML = '';
    if (userWishlist.length === 0) {
      const emptyRow = document.createElement('tr');
      const emptyCell = document.createElement('td');
      emptyCell.setAttribute('colspan', 3);
      emptyCell.textContent = 'No books in the wishlist.';
      emptyRow.appendChild(emptyCell);
      tableBody.appendChild(emptyRow);
    } else {
      userWishlist.forEach(book => {
        const tr = document.createElement('tr');
        const titleCell = document.createElement('td');
        const authorCell = document.createElement('td');
        const deleteCell = document.createElement('td');

        titleCell.textContent = book.title;
        authorCell.textContent = book.author;

        // Create a delete button
        const deleteButton = document.createElement('button');
        deleteButton.textContent = 'Delete';
        deleteButton.className = 'delete-btn';
        deleteButton.onclick = () => deleteBookFromWishlist(book.id);

        deleteCell.appendChild(deleteButton);
        tr.appendChild(titleCell);
        tr.appendChild(authorCell);
        tr.appendChild(deleteCell);
        tableBody.appendChild(tr);
      });
    }
  }

  // Delete a book from the wishlist
  async function deleteBookFromWishlist(bookId) {
    const URL = `${pythonURI}/api/wishlist/${bookId}?user_id=${userId}`; // Backend endpoint for deleting a book
    try {
      const response = await fetch(URL, {
        ...fetchOptions,
        method: 'DELETE',
      });

      if (!response.ok) {
        const errorData = await response.json();
        throw new Error(errorData.error || `Failed to delete book: ${response.status}`);
      }

      // Refresh wishlist after deletion
      userWishlist = await fetchWishlist();
      displayWishlist();
      document.getElementById('profile-message').textContent = 'Book deleted successfully!';
    } catch (error) {
      console.error('Error deleting book:', error.message);
      document.getElementById('profile-message').textContent = `Error: ${error.message}`;
    }
  }

  // Initialization
  document.addEventListener('DOMContentLoaded', async function () {
    try {
      // Fetch predefined books and populate the dropdown
      predefinedBooks = await fetchPredefinedBooks();
      populateBookDropdown(predefinedBooks);

      // Fetch and display the user's wishlist
      userWishlist = await fetchWishlist();
      displayWishlist();
    } catch (error) {
      console.error('Initialization error:', error.message);
      document.getElementById('profile-message').textContent = `Initialization error: ${error.message}`;
    }
  });
</script>

<style>
  .delete-btn {
    background-color: red;
    color: white;
    border: none;
    padding: 5px 10px;
    cursor: pointer;
    border-radius: 4px;
  }

  .delete-btn:hover {
    background-color: darkred;
  }
</style>
