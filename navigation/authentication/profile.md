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
   <form id="profileForm">
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
     <button type="button" id="submitProfileChanges">Submit Changes</button>
     <br>
     <label for="profilePicture" class="file-icon"> Upload Profile Picture <i class="fas fa-upload"></i> <!-- Replace this with your desired icon -->
     </label>
     <input type="file" id="profilePicture" accept="image/*" onchange="saveProfilePicture()">
     <div class="image-container" id="profileImageBox">
         <!-- Profile picture will be displayed here -->
     </div>
     <p id="profile-message" style="color: red;"></p>
   </form>
 </div>
</div>

<div class="wishlist-container">
  <h2>My Reading List</h2>
  <div>
    <label for="bookDropdown">Select a Book: </label>
    <select id="bookDropdown">
      <option value="">--Choose a book--</option>
    </select>
    <button id="addToWishlistButton">Add to reading list</button>
  </div>
  <div>
    <table id="wishlistTable">
      <thead>
        <tr>
          <th>Title</th>
          <th>Author</th>
          <th>Status</th>
          <th>Date Added</th>
          <th>Availability</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody id="wishlist"></tbody>
    </table>
    <p id="wishlistMessage" style="color: green;"></p>
  </div>
</div>

<script type="module">
// Import fetchOptions from config.js
import {pythonURI, fetchOptions } from '{{site.baseurl}}/assets/js/api/config.js';
// Import functions from config.js
import { putUpdate, postUpdate, deleteData, logoutUser } from "{{site.baseurl}}/assets/js/api/profile.js";

// Function to update table with fetched data
function updateTableWithData(data) {
   const tableBody = document.getElementById('profileResult');
   tableBody.innerHTML = '';

   data.sections.forEach((section, index) => {
       const tr = document.createElement('tr');
       const themeCell = document.createElement('td');
       const nameCell = document.createElement('td');

       themeCell.textContent = section.theme;
       nameCell.textContent = section.name;

       const trashIcon = document.createElement('i');
       trashIcon.className = 'fas fa-trash-alt trash-icon';
       trashIcon.style.marginLeft = '10px';
       themeCell.appendChild(trashIcon);

       trashIcon.addEventListener('click', async function (event) {
           event.preventDefault();
           const URL = pythonURI + "/api/user/section";
           // Remove the row from the table
           tr.remove();

           const options = {
               URL,
               body: { sections: [section.theme] },
               message: 'profile-message',
           };

           try {
               await deleteData(options);
           } catch (error) {
               document.getElementById('profile-message').textContent = 'Error deleting section: ' + error.message;
           }
       });

      yearCell.classList.add('editable'); // Make year cell editable
      yearCell.innerHTML = `${section.year} <i class="fas fa-pencil-alt edit-icon" style="margin-left: 10px;"></i>`;

       // Make the year cell editable
       yearCell.addEventListener('click', function () {
           const input = document.createElement('input');
           input.type = 'text';
           input.value = section.year;
           input.className = 'edit-input';
           yearCell.innerHTML = '';
           yearCell.appendChild(input);

           input.focus();

           input.addEventListener('blur', async function () {
               const newYear = input.value;
               const URL = pythonURI + "/api/user/section";
               const options = {
                   URL,
                   body: { section: { theme: section.theme, year: newYear } },
                   message: 'profile-message',
               };

               try {
                   await putUpdate(options);
               } catch (error) {
                   document.getElementById('profile-message').textContent = 'Error updating year: ' + error.message;
               }

               yearCell.textContent = newYear;
           });

           input.addEventListener('keydown', function (event) {
               if (event.key === 'Enter') {
                   input.blur();
               }
           });
       });
       tr.appendChild(themeCell);
       tr.appendChild(nameCell);

       tableBody.appendChild(tr);
   });

}

// Function to fetch user profile data
async function fetchUserProfile() {
    const URL = pythonURI + "/api/id/pfp"; // Endpoint to fetch user profile data

    try {
        const response = await fetch(URL, fetchOptions);
        if (!response.ok) {
            throw new Error(`Failed to fetch user profile: ${response.status}`);
        }

        const profileData = await response.json();
        displayUserProfile(profileData);
    } catch (error) {
        // Handle error display or fallback mechanism
    }
}

// Function to display user profile data
function displayUserProfile(profileData) {
    const profileImageBox = document.getElementById('profileImageBox');
    if (profileData.pfp) {
        const img = document.createElement('img');
        img.src = `data:image/jpeg;base64,${profileData.pfp}`;
        img.alt = 'Profile Picture';
        profileImageBox.innerHTML = ''; // Clear existing content
        profileImageBox.appendChild(img); // Append new image element
    } else {
        profileImageBox.innerHTML = '<p>No profile picture available.</p>';
    }

    // Display other profile information as needed
    // Example: Update HTML elements with profileData.username, profileData.email
}

// Function to save profile picture
window.saveProfilePicture = async function () {

    const fileInput = document.getElementById('profilePicture');
    const file = fileInput.files[0];
    if (file) {
        const reader = new FileReader();
        reader.onload = function() {
            const profileImageBox = document.getElementById('profileImageBox');
            profileImageBox.innerHTML = `<img src="${reader.result}" alt="Profile Picture">`;
        };
        reader.readAsDataURL(file);
    }

    if (!file) return;

    try {
        const base64String = await convertToBase64(file);
        await sendProfilePicture(base64String);
    } catch (error) {
        // Handle error display or fallback mechanism
    }
}

// Function to convert file to base64
async function convertToBase64(file) {
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = () => resolve(reader.result.split(',')[1]); // Remove the prefix part of the result
        reader.onerror = error => reject(error);
        reader.readAsDataURL(file);
    });
}

// Function to send profile picture to server
async function sendProfilePicture(base64String) {
   const URL = pythonURI + "/api/id/pfp"; // Adjust endpoint as needed

   // Create options object for PUT request
   const options = {
       URL,
       body: { pfp: base64String },
       message: 'profile-message', // Adjust the message area as needed
       callback: () => {
           // Handle success response as needed
       }
   };

   try {
       await putUpdate(options);
   } catch (error) {
       document.getElementById('profile-message').textContent = 'Error uploading profile picture: ' + error.message;
   }
}

// Function to update UI with new UID and change placeholder
window.updateUidField = function(newUid) {
  const uidInput = document.getElementById('newUid');
  uidInput.value = newUid;
  uidInput.placeholder = newUid;
}

// Function to update UI with new Name and change placeholder
window.updateNameField = function(newName) {
  const nameInput = document.getElementById('newName');
  nameInput.value = newName;
  nameInput.placeholder = newName;
}

// Function to change UID
window.changeUid = async function(uid) {
   if (uid) {
       const URL = pythonURI + "/api/user"; // Adjusted endpoint

       const options = {
           URL,
           body: { uid },
           message: 'uid-message', // Adjust the message area as needed
           callback: () => {
               window.updateUidField(uid);
           }
       };

       try {
           await putUpdate(options);
       } catch (error) {
           document.getElementById('uid-message').textContent = 'Error updating UID: ' + error.message;
       }
   }
}

window.changePassword = async function(password) {
   if (password) {
       const URL = pythonURI + "/api/user"; // Adjusted endpoint

       const options = {
           URL,
           body: { password },
           message: 'password-message', // Adjust the message area as needed
           callback: () => {
               // Handle success response as needed
           }
       };

       try {
           await putUpdate(options);
       } catch (error) {
           document.getElementById('password-message').textContent = 'Error updating password: ' + error.message;
       }
   }
}

// Function to change Name
window.changeName = async function(name) {
   if (name) {
       const URL = pythonURI + "/api/user";
       const options = {
           URL,
           body: { name },
           message: 'name-message',
           callback: () => {
               window.updateNameField(name);
           }
       };
       try {
           await putUpdate(options);
       } catch (error) {
           document.getElementById('name-message').textContent = 'Error updating Name: ' + error.message;
       }
   }
}

// Event listener to trigger updateUid function when UID field is changed
document.getElementById('newUid').addEventListener('change', function() {
    const uid = this.value;
    window.changeUid(uid);

});

// Event listener to trigger updateName function when Name field is changed
document.getElementById('newName').addEventListener('change', function() {
    const name = this.value;
    window.changeName(name);

});

document.getElementById('newPassword').addEventListener('change', function() {
    const password = this.value;
    window.changePassword(password);

});

// Function to fetch Name from backend
window.fetchName = async function() {
    const URL = pythonURI + "/api/user"; // Adjusted endpoint

    try {
        const response = await fetch(URL, fetchOptions);
        if (!response.ok) {
            throw new Error(`Failed to fetch Name: ${response.status}`);
        }

        const data = await response.json();
        return data.name;
    } catch (error) {
        return null;
    }
};

// Function to set placeholders for UID and Name
window.setPlaceholders = async function() {
    const uidInput = document.getElementById('newUid');
    const nameInput = document.getElementById('newName');

    try {
        const uid = await window.fetchUid();
        const name = await window.fetchName();

        if (uid !== null) {
            uidInput.placeholder = uid;
        }
        if (name !== null) {
            nameInput.placeholder = name;
        }
    } catch (error) {
    }
};

// Function to handle profile changes submission
document.getElementById('submitProfileChanges').addEventListener('click', async function () {
  const uid = document.getElementById('newUid').value;
  const password = document.getElementById('newPassword').value;

  if (uid) {
    await window.changeUid(uid);
  }

  if (password) {
    await window.changePassword(password);
  }

  alert("Profile changes submitted successfully!");
  window.location.href = '/bookworms/login';
});

// Call and initializeProfileSetup when DOM content is loaded
document.addEventListener('DOMContentLoaded', async function () {
    try {
        await fetchUserProfile(); // Fetch user profile data
        await setPlaceholders();
    } catch (error) {
        // Handle initialization error gracefully
    }
});

let predefinedBooks = []; // Declare predefinedBooks
let userWishlist = []; // Declare userWishlist

// Fetch predefined books for the dropdown
async function fetchPredefinedBooks() {
  const URL = `${pythonURI}/api/wishlist/books`;
  try {
    const response = await fetch(URL, fetchOptions);
    if (!response.ok) {
      throw new Error(`Failed to fetch predefined books: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    return [];
  }
}

// Populate the dropdown with predefined books
function populateBookDropdown(books) {
  const dropdown = document.getElementById('bookDropdown');
  if (!dropdown) return; // Ensure the element exists
  dropdown.innerHTML = '<option value="">Select a book</option>';
  books.forEach((book) => {
    const option = document.createElement('option');
    option.value = book.id;
    option.textContent = book.title;
    dropdown.appendChild(option);
  });
}

// Fetch the wishlist
async function fetchWishlist() {
  const URL = `${pythonURI}/api/wishlist/`;
  try {
    const response = await fetch(URL, fetchOptions);
    if (!response.ok) {
      throw new Error(`Failed to fetch wishlist: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    return [];
  }
}

// Add a book to the wishlist
async function addBookToWishlist() {
  const dropdown = document.getElementById('bookDropdown');
  const selectedOption = dropdown.options[dropdown.selectedIndex];
  const bookId = selectedOption.value;

  if (!bookId) {
    document.getElementById('profile-message').textContent = 'Please select a book.';
    return;
  }

  const URL = `${pythonURI}/api/wishlist/`;
  const body = {
    book_id: parseInt(bookId), // Ensure it's an integer
  };

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

    document.getElementById('profile-message').textContent = 'Book added successfully!';
    userWishlist = await fetchWishlist(); // Refresh the wishlist after adding a book
    displayWishlist();
  } catch (error) {
    document.getElementById('profile-message').textContent = `Error: ${error.message}`;
  }
}

// Delete a book from the wishlist
async function deleteBookFromWishlist(id) {
  const URL = `${pythonURI}/api/wishlist/${id}`;
  try {
    const response = await fetch(URL, {
      ...fetchOptions,
      method: 'DELETE',
    });

    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(errorData.error || `Failed to delete book: ${response.status}`);
    }

    document.getElementById('profile-message').textContent = 'Book deleted successfully!';
    userWishlist = await fetchWishlist(); // Refresh the wishlist after deletion
    displayWishlist();
  } catch (error) {
    document.getElementById('profile-message').textContent = `Error: ${error.message}`;
  }
}

// Function to update a book in the wishlist
async function updateBookInWishlist(id, newStatus, newAvailability) {
  const URL = `${pythonURI}/api/wishlist/${id}`;
  const body = {
    status: newStatus,
    availability: newAvailability
  };

  try {
    const response = await fetch(URL, {
      ...fetchOptions,
      method: 'PUT',
      body: JSON.stringify(body),
    });

    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(errorData.error || `Failed to update book in wishlist: ${response.status}`);
    }

    document.getElementById('profile-message').textContent = 'Book updated successfully!';
    userWishlist = await fetchWishlist(); // Refresh the wishlist after updating a book
    displayWishlist();
  } catch (error) {
    document.getElementById('profile-message').textContent = `Error: ${error.message}`;
  }
}

// Function to update availability of a book in the wishlist (admin only)
async function updateBookAvailability(id, newAvailability) {
  const URL = `${pythonURI}/api/wishlist/availability/${id}`;
  const body = {
    availability: newAvailability
  };

  try {
    const response = await fetch(URL, {
      ...fetchOptions,
      method: 'PUT',
      body: JSON.stringify(body),
    });

    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(errorData.error || `Failed to update availability: ${response.status}`);
    }

    document.getElementById('profile-message').textContent = 'Availability updated successfully!';
    userWishlist = await fetchWishlist(); // Refresh the wishlist after updating availability
    displayWishlist();
  } catch (error) {
    document.getElementById('profile-message').textContent = `Error: ${error.message}`;
  }
}

// Display the wishlist
async function displayWishlist() {
  const wishlistContainer = document.getElementById('wishlist');
  if (!wishlistContainer) return; // Ensure the element exists
  wishlistContainer.innerHTML = '';

  if (userWishlist.length === 0) {
    const emptyMessage = document.createElement('p');
    emptyMessage.textContent = 'No books in the wishlist.';
    wishlistContainer.appendChild(emptyMessage);
  } else {
    // Sort the wishlist by status
    const sortedWishlist = userWishlist.sort((a, b) => {
      const statusOrder = { 'for later': 1, 'in progress': 2, 'finished': 3 };
      return statusOrder[a.status] - statusOrder[b.status];
    });

    sortedWishlist.forEach((book) => {
      const tr = document.createElement('tr');
      const titleCell = document.createElement('td');
      const authorCell = document.createElement('td');
      const statusCell = document.createElement('td');
      const dateAddedCell = document.createElement('td');
      const availabilityCell = document.createElement('td');
      const actionCell = document.createElement('td');

      titleCell.textContent = book.title;
      authorCell.textContent = book.author;

      // Create a dropdown for status
      const statusDropdown = document.createElement('select');
      ['for later', 'in progress', 'finished'].forEach((status) => {
        const option = document.createElement('option');
        option.value = status;
        option.textContent = status;
        if (status === book.status) {
          option.selected = true;
        }
        statusDropdown.appendChild(option);
      });
      statusDropdown.onchange = () => {
        const newStatus = statusDropdown.value;
        const newAvailability = book.availability;
        updateBookInWishlist(book.id, newStatus, newAvailability);
      };
      statusCell.appendChild(statusDropdown);

      dateAddedCell.textContent = book.date_added; // Date already formatted in backend

      // Create a dropdown for availability
      const availabilityDropdown = document.createElement('select');
      ['available', 'out of stock'].forEach((availability) => {
        const option = document.createElement('option');
        option.value = availability;
        option.textContent = availability;
        if (availability === book.availability) {
          option.selected = true;
        }
        availabilityDropdown.appendChild(option);
      });
      availabilityDropdown.onchange = () => {
        const newAvailability = availabilityDropdown.value;
        updateBookAvailability(book.id, newAvailability);
      };
      availabilityCell.appendChild(availabilityDropdown);

      const deleteButton = document.createElement('button');
      deleteButton.textContent = 'Delete';
      deleteButton.className = 'delete-btn';
      deleteButton.onclick = () => deleteBookFromWishlist(book.id);

      actionCell.appendChild(deleteButton);
      tr.appendChild(titleCell);
      tr.appendChild(authorCell);
      tr.appendChild(statusCell);
      tr.appendChild(dateAddedCell);
      tr.appendChild(availabilityCell);
      tr.appendChild(actionCell);
      wishlistContainer.appendChild(tr);
    });
  }
}

// Attach functions to the global window object
window.addBookToWishlist = addBookToWishlist;
window.deleteBookFromWishlist = deleteBookFromWishlist;

// Initialize the page
document.addEventListener('DOMContentLoaded', async () => {
  try {
    predefinedBooks = await fetchPredefinedBooks();
    populateBookDropdown(predefinedBooks);

    userWishlist = await fetchWishlist();
    displayWishlist();
  } catch (error) {
    document.getElementById('profile-message').textContent = `Initialization error: ${error.message}`;
  }
});

// Add event listener to the "Add to Wishlist" button
document.getElementById('addToWishlistButton').addEventListener('click', addBookToWishlist);

</script>

<style>
  /* General Container Styling */
  .profile-container, .wishlist-container {
    margin: 20px;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 10px;
    background-color: #f9f9f9;
  }

  .wishlist-container {
    background-color: #d2b48c; /* Light brown */
    color: black; /* Set font color to black */
  }

  /* Card Styling */
  .card {
    padding: 20px;
    border: 1px solid #ddd;
    border-radius: 10px;
    background-color: #fff;
  }

  /* Button Styling */
  button {
    padding: 10px 20px;
    border: none;
    border-radius: 25px;
    background-color: #8b4513; /* Solid brown */
    color: white;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.3s ease;
  }

  button:hover {
    background-color: #deb887; /* Light brown */
    transform: scale(1.05);
  }

  button.delete-btn {
    background-color: #b22222; /* Solid red for delete */
    padding: 5px 10px; /* Make delete button smaller */
  }

  button.delete-btn:hover {
    background-color: #ff6347; /* Light red */
  }

  /* Table Styling */
  .wishlist-container table {
    width: 100%;
    border-collapse: collapse;
    font-family: Arial, sans-serif; /* Updated font for better readability */
    color: #333; /* Darker font color */
  }

  .wishlist-container th, .wishlist-container td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
    background-color: #f2f2f2; /* Set background color for all cells */
  }

  .wishlist-container th {
    background-color: #f2f2f2;
  }
</style>