---
layout: page
title: Tailored Tales
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
        <input type="url" id="cover_image_url" name="cover_image_url" placeholder="Enter image URL" required>
        <button type="submit">Add Book</button>
        <button type="reset" class="start_over">Start Over</button>
    </div>
</form>
</div>

<script type="module">
    import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';

    document.getElementById('book-form').addEventListener('submit', async function(event) {
        event.preventDefault();

        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const coverImageUrl = document.getElementById('cover_image_url').value;

        const bookData = {
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_image_url: coverImageUrl
        };

        try {
            const response = await fetch(`${pythonURI}/api/suggest`, {  // Use /api/suggest endpoint
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(bookData)
            });

            if (!response.ok) {
                throw new Error('Failed to add book: ' + response.statusText);
            }

            const result = await response.json();
            alert('Book added successfully!');
            console.log("Book added successfully")
            document.getElementById('book-form').reset();
            fetchBooks();  // Refresh book list
        } catch (error) {
            console.error('Error adding book:', error);
            alert('Error adding book: ' + error.message);
        }
    });

    async function fetchRandomBook() {
        try {
            const response = await fetch(`${pythonURI}/api/suggest/random`);  // Use /api/suggest/random for GET
            if (!response.ok) {
                throw new Error('Failed to fetch random book: ' + response.statusText);
            }
            const book = await response.json();
            console.log(book);
        } catch (error) {
            console.error('Error fetching random book:', error);
        }
    }

    fetchRandomBook();
</script>