---
layout: page 
title: Reaction
permalink: /reaction/
---

<style>
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
    }

    form {
        margin-bottom: 20px;
    }

    .container {
        max-width: 800px;
        margin: 30px auto;
        padding: 20px;
        background-color: #e6be9a;
        border-radius: 8px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    }

    h1, h2, h3 {
        font-family: 'Arial', sans-serif;
        color: #4A4A4A;
    }

    h2 {
        margin: 20px 0 10px;
        font-size: 1.5em;
        color: #4A4A4A;
    }

    label {
        display: block;
        margin-bottom: 5px;
        font-weight: bold;
        color: #333;
    }

    input, select, button {
        width: 100%;
        padding: 10px;
        margin-bottom: 15px;
        border: 1px solid #ccc;
        border-radius: 5px;
        font-size: 16px;
    }

    button {
        background-color: #A57E5A;
        color: white;
        font-size: 16px;
        cursor: pointer;
        border: none;
    }

    button:hover {
        background-color: #8F5A3E;
        transition: 0.3s;
    }

    .results {
        margin-top: 20px;
        color: black;
    }

    .emotion-item {
        background-color: #F8F0E3;
        margin: 10px 0;
        padding: 15px;
        border-radius: 5px;
        border: 1px solid #E0C7A0;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
</style>

<input type="hidden" id="userId" value="1"> 

<div>
    <p> Share how these books made you feel! </p>
</div>

<div class="container">
    <form id="addEmotionForm">
        <label for="bookDropdown">Select Book</label>
        <select id="bookDropdown" required>
            <option value="">Select a book</option>
        </select>
        <label for="addReactionType">Select Reaction</label>
        <select id="addReactionType" required>
            <option value="">Select Reaction</option>
            <option value="🎉">🎉 Happy</option>
            <option value="👍">👍 Good</option>
            <option value="❤️">❤️ Loved</option>
            <option value="😂">😂 Hilarious</option>
            <option value="😢">😢 Sad</option>
            <option value="🔥">🔥 Hot</option>
            <option value="😎">😎 Cool</option>
            <option value="🤔">🤔 Ponderous</option>
            <option value="💔">💔 Heart Broken</option>
            <option value="🎈">🎈 Drifting Away</option>
            <option value="✨">✨ Magical</option>
            <option value="🍀">🍀 Lucky</option>
            <option value="🌈">🌈 Rainbow</option>
        </select>
        <button type="submit">Add Emotion</button>
    </form>
    <!-- <button id="getBookReactionsButton">Get Book Reactions</button> -->
    <button id="getUserReactionsButton">Get My Reactions</button>
    <button id="resetUserReactionsButton">Reset My Reactions</button>
    <div id="addedEmotions" class="results"></div>
    <div id="reactionsList" class="results"></div>
</div>

<script type="module">
    import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';

    document.addEventListener('DOMContentLoaded', async () => {
        try {
            const books = await fetchBooks();
            populateBookDropdown(books);
        } catch (error) {
            console.error('Error fetching books:', error);
        }
    });

    async function fetchBooks() {
        const URL = `${pythonURI}/api/wishlist/books`;
        try {
            const response = await fetch(URL, fetchOptions);
            if (!response.ok) throw new Error(`Failed to fetch books: ${response.status}`);
            return await response.json();
        } catch (error) {
            console.error('Error:', error);
            return [];
        }
    }

    function populateBookDropdown(books) {
        const dropdown = document.getElementById('bookDropdown');
        dropdown.innerHTML = '<option value="">Select a book</option>';
        books.forEach((book) => {
            const option = document.createElement('option');
            option.value = JSON.stringify({ id: book.id, title: book.title, author: book.author });
            option.textContent = `${book.title} by ${book.author}`;
            dropdown.appendChild(option);
        });
    }

    document.getElementById('getBookReactionsButton').addEventListener('click', async () => {
        const bookDropdown = document.getElementById('bookDropdown');
        const selectedOption = bookDropdown.options[bookDropdown.selectedIndex];

        if (!selectedOption.value) {
            showStatusMessage('Please select a book.', false);
            return;
        }

        const bookData = JSON.parse(selectedOption.value);
        const bookId = bookData.id;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/${bookId}`, fetchOptions);
            if (!response.ok) throw new Error(await response.text());

            const data = await response.json();
            displayReactions(data.emotions);
        } catch (error) {
            showStatusMessage(`Failed to fetch reactions: ${error.message}`, false);
        }
    });

    document.getElementById('getUserReactionsButton').addEventListener('click', async () => {
        const userId = document.getElementById('userId').value;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/user/${userId}`, fetchOptions);
            if (!response.ok) throw new Error(await response.text());

            const data = await response.json();
            displayReactions(data.emotions);
        } catch (error) {
            showStatusMessage(`Failed to fetch reactions: ${error.message}`, false);
        }
    });

    document.getElementById('resetUserReactionsButton').addEventListener('click', async () => {
        const userId = document.getElementById('userId').value;

        if (!confirm("Are you sure you want to reset all your reactions? This cannot be undone.")) return;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/reset_reactions/${userId}`, {
                ...fetchOptions,
                method: 'DELETE',
            });

            if (!response.ok) throw new Error(await response.text());

            document.getElementById('reactionsList').innerHTML = ''; // Clear UI
            showStatusMessage('All your reactions have been reset.');
        } catch (error) {
            showStatusMessage(`Failed to reset reactions: ${error.message}`, false);
        }
    });

    function displayReactions(emotions) {
        const reactionsList = document.getElementById('reactionsList');
        reactionsList.innerHTML = '';

        if (!emotions.length) {
            reactionsList.innerHTML = '<p>No reactions found.</p>';
            return;
        }

        emotions.forEach(emotion => {
            const emotionItem = document.createElement('div');
            emotionItem.className = 'emotion-item';
            emotionItem.innerHTML = `
                <span>📖 <b>${emotion.title_id}</b> by ${emotion.author_id} — Reaction: <span class="reaction-text">${emotion.reaction_type}</span></span>
            `;
            reactionsList.appendChild(emotionItem);
        });
    }
</script>