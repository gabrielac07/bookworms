---
layout: page
title: Suggest
permalink: /bookadd/
---
<style>
    .container {
        max-width: 600px;
        margin: 50px auto;
        padding: 20px;
        background-color: #E8C5A4;
        border-radius: 8px;
    }

    h1 {
        background: #a57e5a;
        padding: 50px;
        font-size: 2em;
        text-align: center;
        color: #000000;
    }

    h2 {
        margin: 20px 0 10px;
        font-size: 1.5em;
        color: black;
    }

    label {
        display: block;
        margin-bottom: 5px;
        font-weight: bold;
        color: black;
    }

    input, textarea, select {
        width: 100%;
        padding: 10px;
        margin-bottom: 20px;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 16px;
    }

    button {
        padding: 10px 15px;
        font-size: 18px;
        margin: 10px 0;
        border: none;
        color: white;
        background-color: #db6f3d;
        border-radius: 4px;
        cursor: pointer;
    }

    button:hover {
        background-color: #ed976f;
        transition: 0.3s;
    }

    .start_over {
        margin-top: 20px;
        color: white;
        border: none;
        padding: 10px 15px;
        cursor: pointer;
        border-radius: 4px;
    }

    .start_over:hover {
        background-color: #a57e5a;
    }
</style>

<div class="container">
<h1>Add your favorite books here!</h1>
<form id="book-form">
    <div>
        <h2>Book Title</h2>
        <label for="title">Title</label>
        <input type="text" id="title" name="title" placeholder="Enter book title" required>
        <h2>Author</h2>
        <label for="author">Author</label>
        <input type="text" id="author" name="author" placeholder="Enter author's name" required>
        <h2>Genre</h2>
        <label for="genre">Genre</label>
        <select id="genre" name="genre" required>
            <option value="">Select Genre</option>
            <option value="Classics">Classics</option>
            <option value="Fantasy">Fantasy</option>
            <option value="Nonfiction">Nonfiction</option>
            <option value="Historical Fiction">Historical Fiction</option>
            <option value="Suspense/Thriller">Suspense/Thriller</option>
            <option value="Romance">Romance</option>
            <option value="Dystopian">Dystopian</option>
            <option value="Mystery">Mystery</option>
        </select>
        <h2>Description</h2>
        <label for="description">Description</label>
        <textarea id="description" name="description" rows="5" placeholder="Enter a brief description" required></textarea>
        <h2>Cover Image</h2>
        <label for="cover_image_url">Cover Image URL</label>
        <input type="url" id="cover_image_url" name="cover_image_url" placeholder="Enter image URL">
        <p>OR</p>
        <label for="cover_image_upload">Upload Cover Image</label>
        <input type="file" id="cover_image_upload" name="cover_image_upload" accept="image/*">
        <button type="submit">Add Book</button>
        <button type="reset" class="start_over">Start Over</button>
    </div>
</form>
</div>

<div id="books-list"></div>

<script type="module">
    // Import server URI and standard fetch options
    import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';

    /**
     * Handle form submission for adding a book
     * Add Book Form Button: Submit book data to the backend API
     */
    document.getElementById('book-form').addEventListener('submit', async function(event) {
        event.preventDefault();

        // Extract data from the form
        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const coverImageUrl = document.getElementById('cover_image_url').value;
        const coverImageFile = document.getElementById('cover_image_upload').files[0];

        // Prepare book data to send
        const bookData = {
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_image_url: coverImageUrl
        };

        // If a file is uploaded, convert it to a base64 string
        if (coverImageFile) {
            const reader = new FileReader();
            reader.onloadend = async function() {
                bookData.cover_image_url = reader.result; // Use base64 string for cover image

                // Send the book data to the backend API
                await submitBookData(bookData);
            };
            reader.readAsDataURL(coverImageFile); // Read file as base64
        } else {
            // If no file, send the data with the URL entered
            await submitBookData(bookData);
        }
    });

    /**
     * Function to send book data to the backend
     * @param {Object} bookData - Book details to send
     */
    async function submitBookData(bookData) {
        try {
            const response = await fetch(`${pythonURI}/api/suggest/books`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(bookData) // Send the book data as JSON
            });

            if (!response.ok) {
                throw new Error('Failed to add book: ' + response.statusText);
            }

            // If successful, parse the response and alert the user
            const result = await response.json();
            alert('Book added successfully!');
            document.getElementById('book-form').reset(); // Reset the form after submission
        } catch (error) {
            console.error('Error adding book:', error);
            alert('Error adding book: ' + error.message);
        }
    }

    async function fetchBooks() {
    try {
        const response = await fetch(`${pythonURI}/api/suggest/books`);
        if (!response.ok) {
            throw new Error('Failed to fetch books: ' + response.statusText);
        }
        const books = await response.json();

        // Display books on the page
        const booksList = document.getElementById('books-list');
        booksList.innerHTML = ''; // Clear any existing list items
        books.forEach(book => {
            const bookElement = document.createElement('div');
            bookElement.className = 'book-item';
            bookElement.innerHTML = `
                <h3>${book.title}</h3>
                <p><strong>Author:</strong> ${book.author}</p>
                <p><strong>Genre:</strong> ${book.genre}</p>
                <p><strong>Description:</strong> ${book.description}</p>
                <img src="${book.cover_image_url}" alt="Cover image" width="100">
            `;
            booksList.appendChild(bookElement);
        });
    } catch (error) {
        console.error('Error fetching books:', error);
    }
}

// Call the function to fetch books on page load
fetchBooks();

</script>
