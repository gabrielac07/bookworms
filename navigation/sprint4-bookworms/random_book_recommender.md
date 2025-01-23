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
            <button onclick="getRandomBook()">Get a Book!</button>
        </div>
        <div id="book_display" class="book_details" style="display: none;">
            <div class="book_cover"><img id="book_cover" alt="Book Cover"></div>
            <h2 id="book_title"></h2>
            <h3 id="book_author"></h3>
            <p id="book_description" class="description"></p>
            <button class="start_over" onclick="startOver()">Get a Different Book</button>
        </div>
        <!--This section is the display for adding a bookrec-->
        <div id="add_bookrec">
            <button onclick="addBookRec()">Add a Book Reccomendation!<button>
            <div id="input_bookrec" class="bookrec_table" style="display: none;">
            <!--In progress-->
            </div>
        </div>
    </div>
<script>
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
    }
    //
    function getRandomBook() {
    //Get the selected genre from the dropdown
    //const genre = document.getElementById("genre").value;
    const genreKey = document.getElementById("genre").value;
    const query = genreMap[genreKey] || "fiction"; // Fallback to "fiction" if genre not mapped
    //Build the API URL with the selected genre as a query parameter
    //const apiUrl = `${pythonURI}/api/random_book?genre=${encodeURIComponent(query)}`;
    const apiUrl = `http://127.0.0.1:8887/api/random_bookrec?genre=${encodeURIComponent(query)}`;
    //Fetch data from the backend API
    fetch(apiUrl) // Flask server endpoint
        .then((response) => {
                if (!response.ok) {
                    throw new Error('No books found for the selected genre.');
                }
                return response.json();
        })
        .then((book) => {
            displayBook(book); // Display the book details on the page
        })
        .catch((error) => { // Catch -> handles any error during execution
            console.error("Error fetching data:", error);
            alert("An error occurred while fetching the book. Please try again.");
        });
    }
    function displayBook(book) {
        const { title, author, description, image_cover } = book;
        // Update the DOM (Document Object Model) with book details
        document.getElementById("book_title").innerText = title;
        document.getElementById("book_author").innerText = `By: ${author}`;
        document.getElementById("book_description").innerText = description;
        // Book cover display
        document.getElementById("book_cover").src = image_cover;
        document.getElementById("book_cover").style.display = image_cover ? "block" : "none";      
        // Hide the genre selection and show the book details
        document.getElementById("genre_selection").style.display = "none";
        document.getElementById("book_display").style.display = "block";
    }
    function startOver() {
        // Reset to the initial view
        document.getElementById("genre_selection").style.display = "block";
        document.getElementById("book_display").style.display = "none";
    }
</script>
    <!--//The genreMap object maps the dropdown values (nonfiction, historical_fiction, etc.) to terms recognized by the Google Books API (e.g., "nonfiction", "thriller").
    const genreMap = {
        nonfiction: "nonfiction",
        historical_fiction: "historical fiction",
        suspense_thriller: "thriller",
        fantasy: "fantasy",
        romance: "romance",
        dystopian: "dystopian",
        classic: "classics",
        mystery: "mystery"
    };
        function getRandomBook() {
        const genreKey = document.getElementById("genre").value;
        const query = genreMap[genreKey] || "fiction"; // Fallback to "fiction" if genre not mapped
        // Call the Google Books API - UPDATE: Google API does not sort books based on genre so I will have to find a different API
        fetch(`https://www.googleapis.com/books/v1/volumes?q=subject:${query}`)
            .then(response => response.json())
            .then(data => {
                if (data.items && data.items.length > 0) {
                    // Pick a random book from the results
                    const randomIndex = Math.floor(Math.random() * data.items.length);
                    const book = data.items[randomIndex];
                    displayBook(book);
                } else {
                    alert("No books found for this genre. Please try another genre.");
                }
            })
            .catch(error => {
                console.error("Error fetching book data:", error);
                alert("An error occurred while fetching books. Please try again.");
            });
    }
    function displayBook(book) {
        const title = book.volumeInfo.title || "No title available";
        const authors = book.volumeInfo.authors ? book.volumeInfo.authors.join(", ") : "Unknown author";
        const description = book.volumeInfo.description || "No summary available";
        const thumbnail = book.volumeInfo.imageLinks ? book.volumeInfo.imageLinks.thumbnail : "";
        // Show the book details
        document.getElementById("book_cover").src = thumbnail;
        document.getElementById("book_cover").style.display = thumbnail ? "block" : "none";
        document.getElementById("book_title").innerText = title;
        document.getElementById("book_author").innerText = `By: ${authors}`;
        document.getElementById("book_description").innerText = description;
        // Hide the genre selection and show the book details
        document.getElementById("genre_selection").style.display = "none";
        document.getElementById("book_display").style.display = "block";
    }
    function startOver() {
        // Reset to the initial view
        document.getElementById("genre_selection").style.display = "block";
        document.getElementById("book_display").style.display = "none";
    }-->
            <!--this is typing an input in the HTML segment<form>
                <label for="input_genre"><strong>What genre do you want to read?</strong>
                <div>
                    Choose from a genre from this list:
                    <ul>
                        <li>nonfiction</li>
                        <li>historical fiction</li>
                        <li>suspense/thriller</li>
                        <li>fantasy</li>
                        <li>romance</li>
                        <li>action</li>
                        <li>classics</li>
                        <li>mystery</li>
                    </ul>
                </div>
                </label><br>
                <input type="text" id="input_genre" name="input_genre"><br>
            </form>-->
</html>