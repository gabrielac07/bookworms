---
layout: page 
title: Book Roulette
permalink: /random_book_recommender/
---
<style>
    .container {
        max-width: 800px;
        margin: 50px auto;
        padding: 20px;
        background-color: #E8C5A4;
        border-radius: 8px;
        text-align: center;
    }

    h1 {
        background: #a57e5a/*#6e8a60*/;
        padding: 50px;
        font-size: 2em;
        text-align: center;
    }

    h2 {
        margin: 20px;
        font-size: 1.5em:
    }

    /*input {
        padding: 15px;
    }*/

    ul {
        list-style-position: inside;
        gap: 16px;
        font-size: 18px;
        color: #E5E7EB;
    }

    select, button {
        padding: 10px 15px;
        font-size: 25px;
        margin: 10px 5px;
        border: 1px solid;
        color: white;
        background-color: #a57e5a;
        border-color: #500A0A;
        border-radius: 4px;
        cursor: pointer;
    }

    select:focus, button:hover {
        background-color: #500A0A/*#72db8e*/;
        transition: 0.3s;   
    }

    .book_details {
        margin-top: 20px;
        text-align: center;
        display: flex;
        align-items: center;
    }

    .book_cover {
        max-width: 200px;
        margin: 10px auto;
        display: flex;
        align-items: center;
        border-width: 8px;
        border-color: #eda579;
    }

    .description {
        font-size: 0.9em;
        margin: 10px 0;
    }

    .start_over {
        margin-top: 20px;
        /*background-color:*/
        color: white;
        border: none;
        padding: 10px 15px;
        cursor: pointer;
        border-radius: 4px;
    }

    .start_over:hover {
        /*background-color:*/
    }

    input, textarea {
        width: 75%;
        padding: 10px;
        margin-bottom: 10px;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 14px;
    }

    table {
        color: #E5E7EB;
        background: #a57e5a;
    }

    th {
        background-color: #500A0A;
    }
</style>
<html>
    <div class="container">
        <h1>Welcome to your Random Book Recommender!</h1>
        <div id="genre_selection">
            <h2>What genre do you want to read?</h2>
            <select id="genre">
                <option value="nonfiction">Nonfiction</option>
                <option value="historical_fiction">Historical Fiction</option>
                <option value="suspense_thriller">Suspense/Thriller</option>
                <option value="fantasy">Fantasy</option>
                <option value="romance">Romance</option>
                <option value="dystopian">Dystopian</option>
                <option value="classic">Classics</option>
                <option value="mystery">Mystery</option>
            </select>
            <button id="getRandomBookButton" onclick="getRandomBook()">Get a Book!</button>
        </div>
        <div id="book_display" class="book_details" style="display: none;">
            <div class="book_cover"><img id="book_cover" alt="Book Cover"></div>
            <h2 id="book_title"></h2>
            <h3 id="book_author"></h3>
            <p id="book_description" class="description"></p>
            <button class="start_over" id="startOver" onclick="startOver()">Get a Different Book</button>
        </div>
        <!--This section is the display for adding a bookrec-->
        <div id="addBookRec">
            <button onclick="inputBookRec()">Add a Book Recommendation!</button>
        </div>
        <div id="input_bookrec" class="bookrec_table" style="display: none;">
            <form id="bookRecForm">
                <p><label>
                    Book Title:
                    <input type="text" name="title" id="title" placeholder="Enter Book Title" required>
                </label></p>
                <p><label>
                    Author:
                    <input type="text" name="author" id="author" placeholder="Enter Book Author" required>
                </label></p>
                <p><label>
                    Genre:
                    <!--<input type="text" name="genre" id="genre" required>-->
                    <select name="genre" id="book_genre" required>
                        <option value="nonfiction">Nonfiction</option>
                        <option value="historical_fiction">Historical Fiction</option>
                        <option value="suspense_thriller">Suspense/Thriller</option>
                        <option value="fantasy">Fantasy</option>
                        <option value="romance">Romance</option>
                        <option value="dystopian">Dystopian</option>
                        <option value="classic">Classics</option>
                        <option value="mystery">Mystery</option>
                    </select>
                </label></p>
                <p><label>
                    Description:
                    <textarea type="text" name="description" rows="5" id="description" placeholder="Enter a short summary of the book" required></textarea>
                </label></p>
                <p><label>
                    Book Cover Image URL:
                    <input type="url" name="cover_url" id="cover_url" placeholder="Enter Book Cover Image URL" required>
                </label></p>
                <p>
                    <button type="submit">Add Book</button>
                    <button type="reset" class="clear_form">Reset</button>
                </p>
            </form>
        </div>
    </div>
    <div id="book_table" class="container">
        <h1>Added Book Reccomendations</h1>
        <br>
        <div id="book_table_content">
            <p style="color: #500A0A">No books added yet. Click "Add a  Book Reccomendation" and fill out the form to start adding more books to our recommendations pool!</p>
        </div>
    </div>

<script type="module">
    import {pythonURI, fetchOptions} from '{{site.baseurl}}/assets/js/api/config.js';
    //The genreMap object maps the dropdown values (nonfiction, historical_fiction, etc.) to terms recognized by the bookdb API (e.g., "Nonfiction", "Suspense/Thriller").

    const genreMap = {
        nonfiction: "Nonfiction",
        historical_fiction: "Historical Fiction",
        suspense_thriller: "Suspense/Thriller",
        fantasy: "Fantasy",
        romance: "Romance",
        dystopian: "Dystopian",
        classic: "Classics",
        mystery: "Mystery"
    };
    //
    //let lastAddedBookId = null; //for deleting the book by last ID
    //let lastAddedBookData = null; //for updating the last book

    function getRandomBook() {
    //Get the selected genre from the dropdown
    //const genre = document.getElementById("genre").value;
    const genreKey = document.getElementById("genre").value;
    const query = genreMap[genreKey] || "Nonfiction"; // Fallback to "fiction" if genre not mapped
    //
    //Build the API URL with the selected genre as a query parameter
    //const apiUrl = `${pythonURI}/api/random_book?genre=${encodeURIComponent(query)}`;
    const apiUrl = `${pythonURI}/api/random_bookrec?genre=${encodeURIComponent(query)}`;
    //Fetch data from the backend API
    fetch(apiUrl, fetchOptions) // Flask server endpoint
        .then((response) => {

                if (!response.ok) {
                    throw new Error('No books found for the selected genre.');
                }
                return response.json();
            })
            .then((book) => {
                displayBook(book); // Display the book details on the page
            })
            .catch((error) => {
                console.error("Error fetching data:", error);
                alert("An error occurred while fetching the book. Please try again.");
            });
    }
    function displayBook(book) {
        const { title, author, description, image_cover } = book;
        document.getElementById("book_title").innerText = title;
        document.getElementById("book_author").innerText = `By: ${author}`;
        document.getElementById("book_description").innerText = description;
        document.getElementById("book_cover").src = image_cover;
        document.getElementById("book_cover").style.display = image_cover ? "block" : "none";
        document.getElementById("genre_selection").style.display = "none";
        document.getElementById("book_display").style.display = "block";
    }
    function startOver() { //redirects the book display back to the default display
        document.getElementById("genre_selection").style.display = "block";
        document.getElementById("book_display").style.display = "none";
    }

    //Adding, Updating, Deleting Book Reccomendations Section

    // Function to display the book recommendation input form
    function inputBookRec() {
        document.getElementById("input_bookrec").style.display = "block";
        document.getElementById("add_bookrec").style.display = "none";
    }

    // Function to add a book recommendation
    document.getElementById('input_bookrec').addEventListener('submit', async function(event) {
        event.preventDefault();

        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('book_genre').value;
        const description = document.getElementById('description').value;
        const coverImageUrl = document.getElementById('cover_url').value;

        const bookData = {
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_url: coverImageUrl
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
                throw new Error('Failed to add book to reccomendations: ' + response.statusText);
            }

            const result = await response.json();
            console.log("Book reccomendation added successfully")
            alert('Book added successfully!');
            document.getElementById('bookRecForm').reset();
            fetchBooks();  // Refresh book list
        } catch (error) {
            console.error('Error adding book to reccomendations:', error);
            alert('Error adding book to reccomendations: ' + error.message);
        }
    });

    // Function to delete the last entered book recommendation
    async function deleteBook(title) {
        if (confirm(`Are you sure you want to delete "${title}"?`)) {
            try {
                const response = await fetch(`${pythonURI}/api/suggest`, {
                    ...fetchOptions,
                    method: 'DELETE',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ title }) // Pass title as an object
                });

                if (!response.ok) {
                    throw new Error('Failed to delete book: ' + response.statusText);
                }

                console.log("Book deleted successfully");
                alert('Book deleted successfully!');
                fetchBooks(); // Refresh the book list
            } catch (error) {
                console.error('Error deleting book:', error);
                alert('Error deleting book: ' + error.message);
            }
        } else {
            alert("Deletion canceled");
        }
    }
    // Function to update the last added book recommendation
    async function updateBook(title) {
        const bookContainer = Array.from(document.querySelectorAll('.book'))
            .find(book => book.querySelector('td:nth-child(2)').innerText === title);

        if (!bookContainer) {
            alert('Book not found for update.');
            return;
        }

        const currentTitle = bookContainer.querySelector('td:nth-child(2)').innerText;
        const currentAuthor = bookContainer.querySelector('td:nth-child(3)').innerText;
        const currentGenre = bookContainer.querySelector('td:nth-child(4)').innerText;
        const currentDescription = bookContainer.querySelector('td:nth-child(5)').innerText;
        const currentCoverUrl = bookContainer.querySelector('img').src;

    // Replace static fields with editable inputs
    bookContainer.innerHTML = `
        <td><input type="text" id="edit-cover-url" value="${currentCoverUrl}" placeholder="Cover URL"></td>
        <td><input type="text" id="edit-title" value="${currentTitle}" placeholder="Title"></td>
        <td><input type="text" id="edit-author" value="${currentAuthor}" placeholder="Author"></td>
        <td>
            <select id="edit-genre">
                <option value="Classics" ${currentGenre === 'Classics' ? 'selected' : ''}>Classics</option>
                <option value="Fantasy" ${currentGenre === 'Fantasy' ? 'selected' : ''}>Fantasy</option>
                <option value="Nonfiction" ${currentGenre === 'Nonfiction' ? 'selected' : ''}>Nonfiction</option>
                <option value="Historical Fiction" ${currentGenre === 'Historical Fiction' ? 'selected' : ''}>Historical Fiction</option>
                <option value="Suspense/Thriller" ${currentGenre === 'Suspense/Thriller' ? 'selected' : ''}>Suspense/Thriller</option>
                <option value="Romance" ${currentGenre === 'Romance' ? 'selected' : ''}>Romance</option>
                <option value="Dystopian" ${currentGenre === 'Dystopian' ? 'selected' : ''}>Dystopian</option>
                <option value="Mystery" ${currentGenre === 'Mystery' ? 'selected' : ''}>Mystery</option>
            </select>
        </td>
        <td><textarea id="edit-description" placeholder="Description">${currentDescription}</textarea></td>
        <td>
            <button id="save-update">OK</button>
            <button id="cancel-update">Cancel</button>
        </td>
    `;

    // Handle Cancel Button
    document.getElementById('cancel-update').addEventListener('click', () => {
        fetchBooks(); // Reload the book list to cancel editing
    });

    document.getElementById('save-update').addEventListener('click', async () => {
        const updatedTitle = document.getElementById('edit-title').value;
        const updatedAuthor = document.getElementById('edit-author').value;
        const updatedGenre = document.getElementById('edit-genre').value;
        const updatedDescription = document.getElementById('edit-description').value;
        const updatedCoverUrl = document.getElementById('edit-cover-url').value;

        const updatedBookData = {
            title: title,
            author: updatedAuthor,
            genre: updatedGenre,
            description: updatedDescription,
            cover_url: updatedCoverUrl
        };

        try {
            const response = await fetch(`${pythonURI}/api/suggest`, {
                ...fetchOptions,
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(updatedBookData)
            });

            if (!response.ok) {
                throw new Error('Failed to update book: ' + response.statusText);
            }

            console.log('Book updated successfully');
            alert('Book updated successfully!');
            fetchBooks(); // Refresh the book list
        } catch (error) {
            console.error('Error updating book:', error);
            alert('Error updating book: ' + error.message);
        }
    });
}
    // create list at bottom
        async function fetchBooks() {
        try {
            const response = await fetch(new URL(`${pythonURI}/api/suggest/book`), fetchOptions); // Fetch all suggested books
            if (!response.ok) {
                throw new Error('Failed to fetch books: ' + response.statusText);
            }

            const books = await response.json();

            // Filter out static books
            const userAddedBooks = books.filter(book => book.title !== 'The Raven Boys' && book.title !== 'Catch-22');

            const bookList = document.getElementById('book_table_content');
            if (userAddedBooks.length === 0) {
                bookList.innerHTML = '<p style="color: #000000">No books added yet. Fill out the form above to start adding your favorite books!</p>';
                return;
            }

        // Render books
        bookList.innerHTML = `
            <table>
                <thead>
                    <tr>
                        <th>Cover</th>
                        <th>Title</th>
                        <th>Author</th>
                        <th>Genre</th>
                        <th>Descriptions</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    ${userAddedBooks.map(book => `
                        <tr class="book">
                            <td><img src="${book.cover_url}" alt="Cover image of ${book.title}" style="max-width: 100px;"></td>
                            <td>${book.title}</td>
                            <td>${book.author}</td>
                            <td>${book.genre}</td>
                            <td>${book.description}</td>
                            <td>
                                <button class="updateButton" data-title="${book.title}">Update</button>
                                <button class="deleteButton" data-title="${book.title}">Delete</button>
                            </td>
                        </tr>
                    `).join('')}
                </tbody>
            </table>
        `;
        
        document.querySelectorAll('.updateButton').forEach(button => {
            button.addEventListener('click', (event) => {
                const title = event.target.dataset.title; // Get the title from data attribute
                updateBook(title);
            });
        });
        document.querySelectorAll('.deleteButton').forEach(button => {
            button.addEventListener('click', (event) => {
                const title = event.target.dataset.title; 
                deleteBook(title);
            });
        });

    } catch (error) {
        console.error('Error fetching books:', error);
    }
}

    document.addEventListener('DOMContentLoaded', () => {
        fetchBooks();
    });

    // Event listeners for various buttons
    document.getElementById('getRandomBookButton').addEventListener('click', getRandomBook);
    document.getElementById('startOver').addEventListener('click', startOver);
    document.getElementById('addBookRec').addEventListener('click', inputBookRec);
</script>
</html>