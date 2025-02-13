---
layout: page 
title: Book Roulette
permalink: /random_book_recommender/
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
        /*border-color: white;*/
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
                    <select name="genre" id="genre" required>
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
                    <button type="button" id="addBookRecButton" onclick="addBookRec()">Done</button>
                    <button type="button" id="deleteBookRecButton" onclick="deleteBookRec()">Delete</button>
                    <button type="button" id="updateBookRecButton" onclick="updateBookRec()">Update</button>
                    <button type="button" id="saveEditsButton" onclick="saveEdits()" style="display: none;">Save Edits</button>
                </p>
            </form>
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
    let lastAddedBookId = null; //for deleting the book by last ID
    let lastAddedBookData = null; //for updating the last book

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
    function addBookRec() { 
        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const coverUrl = document.getElementById('cover_url').value;
        const bookRec = { 
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_url: coverUrl 
        };

        // Send a POST request to add the book recommendation
        fetch(`${pythonURI}/api/add_bookrec`, {
            ...fetchOptions,
            method: 'POST',
            body: JSON.stringify(bookRec)
        })
        .then(response => response.json())
        .then(data => {
            if (data.success) {
                alert('Book recommendation added successfully!');
                lastAddedBookId = data.id; // Store the ID of the added book
                lastAddedBookData = { ...bookRec, id: data.id }; // Store the data of the added book
                clearForm();
            } else {
                alert('Failed to add book recommendation.');
            }
        })
        .catch(error => {
            console.error('Error:', error);
            alert('An error occurred while adding the book recommendation.');
        });
    }
    // Function to delete the last entered book recommendation
    function deleteBookRec() {
        if (lastAddedBookId === null) {
            alert('No book recommendation to delete.');
            return;
        }
        // Send a DELETE request to delete the book recommendation
        fetch(`${pythonURI}/api/delete_bookrec/${lastAddedBookId}`, {
            ...fetchOptions,
            method: 'DELETE'

        })
        .then(response => response.json())
        .then(data => {
            if (data.message === 'Book deleted successfully') {
                alert('Book recommendation deleted successfully!');
                clearForm();
            } else {
                alert('Failed to delete book recommendation.');
            }
        })
        .catch(error => {
            console.error('Error:', error);
            alert('An error occurred while deleting the book recommendation.');
        });
    }
    // Function to update the last added book recommendation
    function updateBookRec() {
        if (lastAddedBookData === null) {
            alert('No book recommendation to update.');
            return;
        }
        // Populate the form with the last added book data
        document.getElementById('title').value = lastAddedBookData.title;
        document.getElementById('author').value = lastAddedBookData.author;
        document.getElementById('genre').value = lastAddedBookData.genre;
        document.getElementById('description').value = lastAddedBookData.description;
        document.getElementById('cover_url').value = lastAddedBookData.cover_url;
        // Display the save edits button
        document.getElementById('saveEditsButton').style.display = 'block';
    }
    // Function to save the edits made to the book recommendation
    function saveEdits() {
        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const coverUrl = document.getElementById('cover_url').value;

        const updatedBookRec = { 
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_url: coverUrl 
        };

        // Send a PUT request to update the book recommendation
        fetch(`${pythonURI}/api/update_bookrec/${lastAddedBookId}`, {
            ...fetchOptions,
            method: 'PUT',
            body: JSON.stringify(updatedBookRec)
        })
        .then(response => response.json())
        .then(data => {
            if (data.success) {
                alert('Book recommendation updated successfully!');
                lastAddedBookData = { ...updatedBookRec, id: lastAddedBookId }; // Update the stored data
                clearForm();
            } else {
                alert('Failed to update book recommendation.');
            }
        })
        .catch(error => {
            console.error('Error:', error);
            alert('An error occurred while updating the book recommendation.');
        });
    }

    // Function to clear the form after submitting a book recommendation
    function clearForm() { 
        document.getElementById('title').value = '';
        document.getElementById('author').value = '';
        document.getElementById('genre').value = '';
        document.getElementById('description').value = '';
        document.getElementById('cover_url').value = '';
        document.getElementById("input_bookrec").style.display = "none";
        document.getElementById("add_bookrec").style.display = "block";
        document.getElementById('saveEditsButton').style.display = 'none';
        lastAddedBookId = null;
        lastAddedBookData = null;
    }
    // Event listeners for various buttons
    document.getElementById('getRandomBookButton').addEventListener('click', getRandomBook);
    document.getElementById('startOver').addEventListener('click', startOver);
    document.getElementById('addBookRec').addEventListener('click', inputBookRec);
    document.getElementById('addBookRecButton').addEventListener('click', addBookRec);
    document.getElementById('deleteBookRecButton').addEventListener('click', deleteBookRec);
    document.getElementById('updateBookRecButton').addEventListener('click', updateBookRec);
    document.getElementById('saveEditsButton').addEventListener('click', saveEdits);
</script>
</html>