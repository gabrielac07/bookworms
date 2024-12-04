---
layout: page
title: User Profile
permalink: /bookworms_profile/
---
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Profile</title>
    <style>
        /* General Styling */
        body {
            background-color: #F7F4EC; /* Pale Cream */
            font-family: Arial, sans-serif;
            color: #4B4A40; /* Warm Charcoal */
            margin: 0;
            padding: 0;
        }

        h2 {
            color: #4B4A40; /* Warm Charcoal */
        }

        .profile-content {
            background-color: #EDE4D9; /* Light Beige */
            padding: 20px;
            border: 1px solid #D3CAB8; /* Soft Sand */
            border-radius: 10px;
            max-width: 600px;
            margin: 40px auto;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }

        /* Styling inputs and buttons */
        input[type="text"],
        input[type="password"],
        input[type="file"],
        textarea {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #D3CAB8; /* Soft Sand */
            border-radius: 5px;
            font-size: 1em;
            background-color: #F7F4EC; /* Pale Cream */
            color: #4B4A40; /* Warm Charcoal */
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        textarea {
            resize: vertical;
            height: 100px;
        }

        button {
            background-color: #C3A382; /* Muted Amber */
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            color: #4B4A40; /* Warm Charcoal */
            cursor: pointer;
            font-size: 1em;
            transition: background-color 0.3s;
        }

        button:hover {
            background-color: #A6947E; /* Subtle Taupe */
        }

        /* Align inputs to fit within the built-in layout */
        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            font-weight: bold;
            display: block;
            margin-bottom: 5px;
            color: #4B4A40; /* Warm Charcoal */
        }
    </style>
</head>
<body>
    <div class="profile-content">
        <h2>User Profile</h2>
        <!-- Username Section -->
        <div class="form-group">
            <label for="username">Username</label>
            <input type="text" id="username" placeholder="Enter your username">
        </div>

        <!-- Password Section -->
        <div class="form-group">
            <label for="password">Password</label>
            <input type="password" id="password" placeholder="Enter your password">
        </div>

        <!-- Profile Photo Section -->
        <div class="form-group">
            <label for="profile-photo">Upload a photo</label>
            <input type="file" id="profile-photo">
        </div>

        <!-- Books Read Section -->
        <div class="form-group">
            <label for="books-read">Books You've Read</label>
            <textarea id="books-read" placeholder="List the books you've read, separated by commas"></textarea>
        </div>

        <!-- Books to Read Section -->
        <div class="form-group">
            <label for="books-to-read">Books You Want to Read</label>
            <textarea id="books-to-read" placeholder="List the books you want to read, separated by commas"></textarea>
        </div>

        <!-- Save Button -->
        <div class="form-group">
            <button onclick="saveProfile()">Save Changes</button>
        </div>
    </div>

    <script>
        // Procedure for saving profile data
        function saveProfile() {
            const username = document.getElementById("username").value;
            const password = document.getElementById("password").value;
            const booksReadInput = document.getElementById("books-read").value;
            const booksToReadInput = document.getElementById("books-to-read").value;

            // Using lists to represent collections of data
            const booksRead = booksReadInput.split(",").map(book => book.trim());
            const booksToRead = booksToReadInput.split(",").map(book => book.trim());

            // Validate input (selection)
            if (!username || !password) {
                alert("Please fill in both username and password!");
                return;
            }

            // Algorithm: Sequencing, selection, and iteration
            displayProfile(username, booksRead, booksToRead);

            // Output for confirmation
            alert("Profile saved successfully!");
        }

        // Procedure to display profile information
        function displayProfile(username, booksRead, booksToRead) {
            console.log("Profile Summary:");
            console.log("Username:", username);
            console.log("Books Read:", booksRead);
            console.log("Books to Read:", booksToRead);

            // Example of visual output
            const summary = `
                <h2>Profile Summary</h2>
                <p><strong>Username:</strong> ${username}</p>
                <p><strong>Books Read:</strong> ${booksRead.join(", ") || "None"}</p>
                <p><strong>Books to Read:</strong> ${booksToRead.join(", ") || "None"}</p>
            `;
            document.body.insertAdjacentHTML("beforeend", summary);
        }
    </script>
</body>
</html>
