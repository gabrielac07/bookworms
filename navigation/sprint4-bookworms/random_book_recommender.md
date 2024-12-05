---
layout: page 
title: Book Recommender
permalink: /random_book_recommender/
---
<style>
    body {
        background-color: black;
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
        background: #a8f0ad;
        padding: 50px;
        font-size: 2em;
    }

    input {
        padding: 15px;
    }

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
        /*Add smth here*/    
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
            <p>What genre do you want to read?</p>
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
            <h2 id="book-title"></h2>
            <h3 id="book_author"></h3>
            <p id="book_summary" class="summary"></p>
            <button class="start-over" onclick="startOver()">Get a Different Book</button>
        </div>
    </div>
</body>
<script>
</script>
</html>
