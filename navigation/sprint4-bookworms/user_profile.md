---
layout: post
title: Profile Settings
permalink: /bookworms_profile/
menu: nav/home.html
search_exclude: true
show_reading_time: false
---

<div class="profile-container">
  <div class="card">
    <form>
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
          </tr>
        </thead>
        <tbody id="wishlistResult">
          <!-- Wishlist books will be dynamically populated -->
        </tbody>
      </table>
      <label for="profilePicture" class="file-icon"> Upload Profile Picture <i class="fas fa-upload"></i></label>
      <input type="file" id="profilePicture" accept="image/*" onchange="saveProfilePicture()">
      <div class="image-container" id="profileImageBox">
        <!-- Profile picture will be displayed here -->
      </div>
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
      return await response.json();
    } catch (error) {
      console.error('Error fetching predefined books:', error.message);
      return [];
    }
  }

  // Populate the book dropdown
  function populateBookDropdown(predefinedBooks) {
    const bookDropdown = document.getElementById('bookDropdown');
    bookDropdown.innerHTML = '';
    predefinedBooks.forEach(book => {
      const option = document.createElement('option');
      option.value = book.id; // Assuming `book` has an `id` property
      option.textContent = `${book.title} by ${book.author}`;
      bookDropdown.appendChild(option);
    });
  }

  // Fetch user's wishlist
  async function fetchWishlist() {
    const URL = `${pythonURI}/api/wishlist/`; // Backend endpoint for fetching user's wishlist
    try {
      const response = await fetch(URL, fetchOptions);
      if (!response.ok) {
        throw new Error(`Failed to fetch wishlist: ${response.status}`);
      }
      return await response.json();
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
    const body = { book_id: parseInt(bookId) }; // Pass only `book_id` as per backend requirements

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
    userWishlist.forEach(book => {
      const tr = document.createElement('tr');
      const titleCell = document.createElement('td');
      const authorCell = document.createElement('td');

      titleCell.textContent = book.title;
      authorCell.textContent = book.author;

      tr.appendChild(titleCell);
      tr.appendChild(authorCell);
      tableBody.appendChild(tr);
    });
  }

  // Upload and save the profile picture
  window.saveProfilePicture = async function () {
    const fileInput = document.getElementById('profilePicture');
    const file = fileInput.files[0];

    if (!file) {
      document.getElementById('profile-message').textContent = 'Please select a profile picture.';
      return;
    }

    const formData = new FormData();
    formData.append('profile_picture', file);
    formData.append('user_id', userId);

    const URL = `${pythonURI}/api/users/upload_picture/`; // Backend endpoint to handle profile picture upload

    try {
      const response = await fetch(URL, {
        ...fetchOptions,
        method: 'POST',
        body: formData,
      });

      if (!response.ok) {
        throw new Error('Failed to upload profile picture');
      }

      const data = await response.json();
      document.getElementById('profileImageBox').innerHTML = `<img src="${data.profile_picture_url}" alt="Profile Picture" />`;
      document.getElementById('profile-message').textContent = 'Profile picture uploaded successfully!';
    } catch (error) {
      console.error('Error uploading profile picture:', error.message);
      document.getElementById('profile-message').textContent = `Error: ${error.message}`;
    }
  };

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
