---
layout: page 
title: Reaction
permalink: /reaction/
---

<style>

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

    .emotion-item div {
        display: flex;
        gap: 5px;
    }

    .update-button, .delete-button {
        background: none;
        border: none;
        font-size: 18px;
        cursor: pointer;
        padding: 5px;
    }

    .update-button:hover {
        color: #f0ad4e;
    }

    .delete-button:hover {
        color: #d9534f;
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
    <div id="addedEmotions" class="results"></div>
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

    document.getElementById('addEmotionForm').addEventListener('submit', async (e) => {
        e.preventDefault();
        const userId = document.getElementById('userId').value;
        const bookDropdown = document.getElementById('bookDropdown');
        const selectedOption = bookDropdown.options[bookDropdown.selectedIndex];

        if (!selectedOption.value) {
            showStatusMessage('Please select a book.', false);
            return;
        }

        const bookData = JSON.parse(selectedOption.value);
        const bookId = bookData.id;
        const bookTitle = bookData.title;
        const authorId = bookData.author;
        const reactionType = document.getElementById('addReactionType').value;

        try {
            const response = await fetch(`${pythonURI}/api/emotion`, {
                ...fetchOptions,
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ user_id: userId, title_id: bookId, author_id: authorId, reaction_type: reactionType }),
            });

            if (!response.ok) throw new Error(await response.text());

            displayAddedEmotion(bookId, bookTitle, authorId, reactionType);
            showStatusMessage('Emotion successfully added!');
        } catch (error) {
            showStatusMessage(`Failed to add emotion: ${error.message}`, false);
        }
    });

    function displayAddedEmotion(bookId, bookTitle, authorId, reactionType) {
        const addedEmotionsDiv = document.getElementById('addedEmotions');
        const emotionItem = document.createElement('div');
        emotionItem.className = 'emotion-item';
        emotionItem.dataset.bookId = bookId;
        emotionItem.dataset.authorId = authorId;
        emotionItem.dataset.reaction = reactionType;

        emotionItem.innerHTML = `
            <span>📖 <b>${bookTitle}</b> by ${authorId} — Reaction: <span class="reaction-text">${reactionType}</span></span>
            <div>
                <button class="update-button">📝</button>
                <button class="delete-button">🗑️</button>
            </div>
        `;

        addedEmotionsDiv.appendChild(emotionItem);
        emotionItem.querySelector('.update-button').addEventListener('click', handleUpdateEmotion);
        emotionItem.querySelector('.delete-button').addEventListener('click', handleDeleteEmotion);
    }

    async function handleUpdateEmotion(event) {
        const userId = document.getElementById('userId').value;
        const emotionItem = event.target.closest('.emotion-item');
        const titleId = emotionItem.dataset.bookId;
        const authorId = emotionItem.dataset.authorId;

        const dropdown = document.createElement('select');
        dropdown.innerHTML = document.getElementById('addReactionType').innerHTML;
        dropdown.value = emotionItem.dataset.reaction;

        const submitButton = document.createElement('button');
        submitButton.textContent = 'Save';
        submitButton.onclick = async () => {
            const newReaction = dropdown.value;
            if (!newReaction) return;

            try {
                const response = await fetch(`${pythonURI}/api/emotion/update`, {
                    ...fetchOptions,
                    method: 'PUT',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ user_id: userId, title_id: titleId, reaction_type: newReaction }),
                });

                if (!response.ok) throw new Error(await response.text());

                emotionItem.dataset.reaction = newReaction;
                emotionItem.querySelector('.reaction-text').textContent = newReaction;
                showStatusMessage('Emotion successfully updated!');

                emotionItem.innerHTML = `
                    <span>📖 <b>${emotionItem.dataset.bookTitle}</b> by ${authorId} — Reaction: <span class="reaction-text">${newReaction}</span></span>
                    <div>
                        <button class="update-button">📝</button>
                        <button class="delete-button">🗑️</button>
                    </div>
                `;

                emotionItem.querySelector('.update-button').addEventListener('click', handleUpdateEmotion);
                emotionItem.querySelector('.delete-button').addEventListener('click', handleDeleteEmotion);
            } catch (error) {
                showStatusMessage(`Failed to update: ${error.message}`, false);
            }
        };

        const actionCell = event.target.parentElement;
        actionCell.innerHTML = '';
        actionCell.appendChild(dropdown);
        actionCell.appendChild(submitButton);
    }

    async function handleDeleteEmotion(event) {
        const userId = document.getElementById('userId').value;
        const emotionItem = event.target.closest('.emotion-item');
        const titleId = emotionItem.dataset.bookId;
        const authorId = emotionItem.dataset.authorId;

        if (!confirm(`Delete emotion for "${emotionItem.dataset.bookTitle}"?`)) return;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/delete`, {
                ...fetchOptions,
                method: 'DELETE',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ user_id: userId, title_id: titleId, author_id: authorId }),
            });

            if (!response.ok) throw new Error(await response.text());

            emotionItem.remove();
            showStatusMessage('Emotion successfully deleted!');
        } catch (error) {
            showStatusMessage(`Failed to delete: ${error.message}`, false);
        }
    }

    function showStatusMessage(message, isSuccess = true) {
        const statusMessage = document.getElementById('statusMessage') || createStatusMessageElement();
        statusMessage.textContent = message;
        statusMessage.style.color = isSuccess ? 'green' : 'red';
        statusMessage.style.display = 'block';
        setTimeout(() => statusMessage.style.display = 'none', 3000);
    }

    function createStatusMessageElement() {
        const statusMessage = document.createElement('div');
        statusMessage.id = 'statusMessage';
        statusMessage.style.position = 'fixed';
        statusMessage.style.top = '10px';
        statusMessage.style.left = '50%';
        statusMessage.style.transform = 'translateX(-50%)';
        statusMessage.style.padding = '10px 20px';
        statusMessage.style.background = 'rgba(255, 255, 255, 0.9)';
        statusMessage.style.border = '1px solid black';
        statusMessage.style.borderRadius = '5px';
        statusMessage.style.fontWeight = 'bold';
        document.body.appendChild(statusMessage);
        return statusMessage;
    }
</script>