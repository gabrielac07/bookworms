---
layout: page 
title: Book Recommender
permalink: /random_book_recommender/
---
<style>
    body {
        background-color: #0B3954;
        text-align: center;
    }
    .container {
        max-width: 600px;
        margin: 50px auto;
        padding: 20px;
        /*background-color:*/
        border-radius: 8px;
    }

    h1 {
        background: #6e8a60;
        padding: 50px;
        font-size: 2em;
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
        border: 1px solid; /*add border color*/
        border-radius: 4px;
        cursor: pointer;
    }

    select:focus, button:hover {
        background-color: #435457;
        transition: 0.3s;   
    }

    .book_details {
        margin-top: 20px
    }

    .book_cover {
        max-width: 200px;
        margin: 10px auto:
    }

    .summary {
        font-size: 0.9em;
        margin: 10px 0;
    }

    .start_over {
        margin-top: 20px;
        /*background-color:*/
        color: white;
        border: none:
        padding: 10px 15px;
        cursor: pointer;
        border-radius: 4px;
    }

    .start_over:hover {
        /*background-color:*/
    }
</style>
<html>
<body>
    <div class="container">
        <h1>Welcome to your Random Book Recommender!</h1>
        <div id="genre_selection">
            <!--this is typing an input<form>
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
            <h2>What genre do you want to read?</h2>
            <select id="genre">
                <option value="nonfiction">Nonfiction</option>
                <option value="historical_fiction">Historical Fiction</option>
                <option value="suspense_thriller">Suspense/Thriller</option>
                <option value="fantasy">Fantasy</option>
                <option value="romance">Romance</option>
                <option value="action">Action</option>
                <option value="classic">Classics</option>
                <option value="mystery">Mystery</option>
            </select>
            <button onclick="getRandomBook()">Get a Book!</button>
        </div>
        <div id="book_display" class="book_details" style="display: none;">
            <img id="book_cover" class="book_cover" alt="Book Cover">
            <h2 id="book_title"></h2>
            <h3 id="book_author"></h3>
            <p id="book_summary" class="summary"></p>
            <button class="start_over" onclick="startOver()">Get a Different Book</button>
        </div>
    </div>
</body>
<script>
    //The genreMap object maps the dropdown values (nonfiction, historical_fiction, etc.) to terms recognized by the Google Books API (e.g., "nonfiction", "thriller").
    const genreMap = {
        nonfiction: "nonfiction",
        historical_fiction: "historical fiction",
        suspense_thriller: "thriller",
        fantasy: "fantasy",
        romance: "romance",
        action: "action",
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
        document.getElementById("book_summary").innerText = description;
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
</html>