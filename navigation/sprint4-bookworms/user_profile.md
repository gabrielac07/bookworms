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
        /* Styling only inputs and buttons */
        input[type="text"],
        input[type="password"],
        input[type="file"],
        textarea {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            font-size: 1em;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        textarea {
            resize: vertical;
            height: 100px;
        }

        button {
            background-color: #ffcb85;
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            color: #4a4a4a;
            cursor: pointer;
            font-size: 1em;
            transition: background 0.3s;
        }

        button:hover {
            background-color: #f5b461; /* Darker orange on hover */
        }

        /* Align inputs to fit within the built-in layout */
        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            font-weight: bold;
            display: block;
            margin-bottom: 5px;
        }
    </style>
</head>
<body>
    <div class="profile-content">
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
        function saveProfile() {
            // Placeholder logic for saving profile data
            const username = document.getElementById("username").value;
            const password = document.getElementById("password").value;
            const booksRead = document.getElementById("books-read").value.split(",").map(book => book.trim());
            const booksToRead = document.getElementById("books-to-read").value.split(",").map(book => book.trim());

            console.log({
                username,
                password,
                booksRead,
                booksToRead
            });

            alert("Profile updated!");
        }
    </script>
</body>
</html>
