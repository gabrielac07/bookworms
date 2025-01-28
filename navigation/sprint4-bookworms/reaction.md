---
layout: page 
title: Book Roulette
permalink: /idk/
---


<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Emotion API Frontend</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        h1 {
            color: #333;
        }
        form {
            margin-bottom: 20px;
        }
        input, select, button {
            margin: 5px 0;
            padding: 8px;
            width: 200px;
        }
        .results {
            margin-top: 20px;
        }
        .emotion-item {
            margin: 5px 0;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
    </style>
</head>
<body>

<h1>Emotion API Frontend</h1>

<!-- User ID (hidden for now) -->
<input type="hidden" id="userId" value="1"> <!-- Example user ID; replace with dynamic value -->

<!-- Add Emotion Form -->
<h2>Add Emotion</h2>
<form id="addEmotionForm">
    <input type="text" placeholder="Title ID" id="addTitleId" required>
    <input type="text" placeholder="Author ID" id="addAuthorId" required>
    <select id="addReactionType" required>
        <option value="">Select Reaction</option>
        <option value="🎉">🎉 Celebration</option>
        <option value="👍">👍 Like</option>
        <option value="❤️">❤️ Love</option>
        <option value="😂">😂 Funny</option>
        <option value="😢">😢 Sad</option>
        <option value="🔥">🔥 Hot</option>
    </select>
    <button type="submit">Add Emotion</button>
</form>

<!-- Display Added Emotions -->
<div id="addedEmotions" class="results"></div>

<!-- Other Options -->
<h2>Other Options</h2>
<!-- Get Reactions for Title -->
<h3>Get Reactions for Title</h3>
<input type="text" placeholder="Title ID" id="getTitleId">
<button id="getTitleButton">Get Reactions</button>
<div id="titleResults" class="results"></div>

<!-- Get Reactions for User -->
<h3>Get Reactions for User</h3>
<input type="number" placeholder="User ID" id="getUserId" value="1">
<button id="getUserButton">Get Reactions</button>
<div id="userResults" class="results"></div>

<!-- Update Emotion -->
<h3>Update Emotion</h3>
<form id="updateEmotionForm">
    <input type="number" placeholder="User ID" id="updateUserId" value="1" required>
    <input type="text" placeholder="Title ID" id="updateTitleId" required>
    <input type="text" placeholder="New Reaction Type" id="updateReactionType" required>
    <input type="text" placeholder="Author ID" id="updateAuthorIdUpdate" required>
    <button type="submit">Update Emotion</button>
</form>

<!-- Delete Emotion -->
<h3>Delete Emotion</h3>
<form id="deleteEmotionForm">
    <input type="number" placeholder="User ID" id="deleteUserId" value="1" required>
    <input type="text" placeholder="Title ID" id="deleteTitleId" required>
    <button type="submit">Delete Emotion</button>
</form>

<!-- Reset Reactions -->
<h3>Reset All Reactions for User</h3>
<form id="resetEmotionForm">
    <input type="number" placeholder="User ID" id="resetUserId" value="1" required>
    <button type="submit">Reset Reactions</button>
</form>

<script>
    const API_URL = 'http://127.0.0.1:8887/api/emotion'; // Update with your API URL

    // Function to add emotion
    document.getElementById('addEmotionForm').addEventListener('submit', async (e) => {
        e.preventDefault();
        const userId = document.getElementById('userId').value; // Get logged in user ID
        const titleId = document.getElementById('addTitleId').value;
        const authorId = document.getElementById('addAuthorId').value;
        const reactionType = document.getElementById('addReactionType').value;

        const response = await fetch(API_URL, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ user_id: userId, title_id: titleId, author_id: authorId, reaction_type: reactionType }),
        });

        const result = await response.json();
        alert(result.message || result.error);

        // Display the added emotion
        if (response.ok) {
            displayAddedEmotion(titleId, authorId, reactionType);
            clearAddEmotionForm();
        }
    });

    // Function to display added emotion
    function displayAddedEmotion(titleId, authorId, reactionType) {
        const addedEmotionsDiv = document.getElementById('addedEmotions');
        const emotionItem = document.createElement('div');
        emotionItem.className = 'emotion-item';
        emotionItem.innerHTML = `Title: ${titleId}, Author: ${authorId}, Reaction: ${reactionType}`;
        addedEmotionsDiv.appendChild(emotionItem);
    }

    // Clear the add emotion form
    function clearAddEmotionForm() {
        document.getElementById('addTitleId').value = '';
        document.getElementById('addAuthorId').value = '';
        document.getElementById('addReactionType').value = '';
    }

    // Function to get reactions for a specific title
    document.getElementById('getTitleButton').addEventListener('click', async () => {
        const titleId = document.getElementById('getTitleId').value;
        const response = await fetch(`${API_URL}/${titleId}`);
        const result = await response.json();
        const resultsDiv = document.getElementById('titleResults');
        resultsDiv.innerHTML = '';

        if (result.emotions) {
            result.emotions.forEach(emotion => {
                const div = document.createElement('div');
                div.textContent = `User ID: ${emotion.user_id}, Reaction: ${emotion.reaction_type}`;
                resultsDiv.appendChild(div);
            });
        } else {
            resultsDiv.textContent = result.error;
        }
    });

    // Function to get reactions for a specific user
    document.getElementById('getUserButton').addEventListener('click', async () => {
        const userId = document.getElementById('getUserId').value;
        const response = await fetch(`${API_URL}/user/${userId}`);
        const result = await response.json();
        const resultsDiv = document.getElementById('userResults');
        resultsDiv.innerHTML = '';

        if (result.emotions) {
            result.emotions.forEach(emotion => {
                const div = document.createElement('div');
                div.textContent = `Title ID: ${emotion.title_id}, Reaction: ${emotion.reaction_type}, Author ID: ${emotion.author_id}`;
                resultsDiv.appendChild(div);
            });
        } else {
            resultsDiv.textContent = result.error;
        }
    });

    // Function to update emotion
    document.getElementById('updateEmotionForm').addEventListener('submit', async (e) => {
        e.preventDefault();
        const userId = document.getElementById('updateUserId').value;
        const titleId = document.getElementById('updateTitleId').value;
        const newReactionType = document.getElementById('updateReactionType').value;
        const authorId = document.getElementById('updateAuthorIdUpdate').value;

        const response = await fetch(`${API_URL}/update`, {
            method: 'PUT',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ user_id: userId, title_id: titleId, reaction_type: newReactionType, author_id: authorId }),
        });

        const result = await response.json();
        alert(result.message || result.error);
    });

    // Function to delete emotion
    document.getElementById('deleteEmotionForm').addEventListener('submit', async (e) => {
        e.preventDefault();
        const userId = document.getElementById('deleteUserId').value;
        const titleId = document.getElementById('deleteTitleId').value;

        const response = await fetch(`${API_URL}/delete`, {
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ user_id: userId, title_id: titleId }),
        });

        const result = await response.json();
        alert(result.message || result.error);
    });

    // Function to reset reactions
    document.getElement
