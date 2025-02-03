---
layout: page 
title: Book Roulette
permalink: /emotion/
---
<head>
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
        <option value="😎">😎 Cool</option>
        <option value="🥳">🥳 Party</option>
        <option value="🤔">🤔 Thinking</option>
        <option value="💔">💔 Broken Heart</option>
        <option value="🎈">🎈 Balloon</option>
        <option value="🌟">🌟 Star</option>
        <option value="✨">✨ Sparkle</option>
        <option value="🍀">🍀 Lucky</option>
        <option value="🌈">🌈 Rainbow</option>
    </select>
    <button type="submit">Add Emotion</button>
</form>

<!-- Display Added Emotions -->
<div id="addedEmotions" class="results"></div>

<!-- Other Options -->
<h2>Other Options</h2>
<select id="otherOptionsDropdown">
    <option value="">Select an Option</option>
    <option value="getReactionsForTitle">Get Reactions for Title</option>
    <option value="getReactionsForUser">Get Reactions for User</option>
    <option value="updateEmotion">Update Emotion</option>
    <option value="deleteEmotion">Delete Emotion</option>
    <option value="resetReactions">Reset Reactions</option>
</select>

<!-- Get Reactions for Title -->
<div id="titleOptions" style="display: none;">
    <h3>Get Reactions for Title</h3>
    <input type="text" placeholder="Title ID" id="getTitleId">
    <button id="getReactionsForTitleButton">Get Reactions</button>
    <div id="titleResults" class="results"></div>
</div>

<!-- Get Reactions for User -->
<div id="userOptions" style="display: none;">
    <h3>Get Reactions for User</h3>
    <input type="number" placeholder="User ID" id="getUserId" value="1">
    <button id="getReactionsForUserButton">Get Reactions</button>
    <div id="userResults" class="results"></div>
</div>

<!-- Update Emotion -->
<div id="updateOptions" style="display: none;">
    <h3>Update Emotion</h3>
    <input type="number" placeholder="User ID" id="updateUserId" value="1" required>
    <input type="text" placeholder="Title ID" id="updateTitleId" required>
    <select id="updateReactionType" required>
        <option value="">Select New Reaction</option>
        <option value="🎉">🎉 Celebration</option>
        <option value="👍">👍 Like</option>
        <option value="❤️">❤️ Love</option>
        <option value="😂">😂 Funny</option>
        <option value="😢">😢 Sad</option>
        <option value="🔥">🔥 Hot</option>
        <option value="😎">😎 Cool</option>
        <option value="🥳">🥳 Party</option>
        <option value="🤔">🤔 Thinking</option>
        <option value="💔">💔 Broken Heart</option>
        <option value="🎈">🎈 Balloon</option>
        <option value="🌟">🌟 Star</option>
        <option value="✨">✨ Sparkle</option>
        <option value="🍀">🍀 Lucky</option>
        <option value="🌈">🌈 Rainbow</option>
    </select>
    <input type="text" placeholder="Author ID" id="updateAuthorIdUpdate" required>
    <button id="updateEmotionButton">Update Emotion</button>
</div>

<!-- Delete Emotion -->
<div id="deleteOptions" style="display: none;">
    <h3>Delete Emotion</h3>
    <input type="number" placeholder="User ID" id="deleteUserId" value="1" required>
    <input type="text" placeholder="Title ID" id="deleteTitleId" required>
    <button id="deleteEmotionButton">Delete Emotion</button>
</div>

<!-- Reset Reactions -->
<div id="resetOptions" style="display: none;">
    <h3>Reset All Reactions for User</h3>
    <input type="number" placeholder="User ID" id="resetUserId" value="1" required>
    <button id="resetReactionsButton">Reset Reactions</button>
</div>

<script type="module">
    import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';
    // const API_URL = 'http://127.0.0.1:8504/api/emotion'; // Update with your API URL

    // Function to add emotion
    document.getElementById('addEmotionForm').addEventListener('submit', async (e) => {
        e.preventDefault();
        const userId = document.getElementById('userId').value; // Get logged in user ID
        const titleId = document.getElementById('addTitleId').value;
        const authorId = document.getElementById('addAuthorId').value;
        const reactionType = document.getElementById('addReactionType').value;

        try {
             const response = await fetch(`${pythonURI}/api/emotion`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ user_id: userId, title_id: titleId, author_id: authorId, reaction_type: reactionType }),
            });
            const result = await response.json();
            if (!response.ok) throw new Error(result.error || 'Failed to add emotion.');

            // Display the added emotion
            displayAddedEmotion(titleId, authorId, reactionType);
            clearAddEmotionForm();
            alert('Emotion added successfully!');
        } catch (error) {
            alert(error.message);
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

    // Handle other options selection
    document.getElementById('otherOptionsDropdown').addEventListener('change', (e) => {
        const selectedOption = e.target.value;

        document.getElementById('titleOptions').style.display = 'none';
        document.getElementById('userOptions').style.display = 'none';
        document.getElementById('updateOptions').style.display = 'none';
        document.getElementById('deleteOptions').style.display = 'none';
        document.getElementById('resetOptions').style.display = 'none';

        if (selectedOption === 'getReactionsForTitle') {
            document.getElementById('titleOptions').style.display = 'block';
        } else if (selectedOption === 'getReactionsForUser') {
            document.getElementById('userOptions').style.display = 'block';
        } else if (selectedOption === 'updateEmotion') {
            document.getElementById('updateOptions').style.display = 'block';
        } else if (selectedOption === 'deleteEmotion') {
            document.getElementById('deleteOptions').style.display = 'block';
        } else if (selectedOption === 'resetReactions') {
            document.getElementById('resetOptions').style.display = 'block';
        }
    });

    // Get Reactions for Title
    document.getElementById('getReactionsForTitleButton').addEventListener('click', async () => {
        const titleId = document.getElementById('getTitleId').value;

        try {
             const response = await fetch(`${pythonURI}/api/emotion/${titleId}`);
            const result = await response.json();
            const titleResultsDiv = document.getElementById('titleResults');

            titleResultsDiv.innerHTML = ''; // Clear previous results
            if (response.ok) {
                result.emotions.forEach(emotion => {
                    const emotionItem = document.createElement('div');
                    emotionItem.innerHTML = `User ID: ${emotion.user_id}, Reaction: ${emotion.reaction_type}`;
                    titleResultsDiv.appendChild(emotionItem);
                });
            } else {
                titleResultsDiv.innerHTML = result.error || 'Error fetching reactions.';
            }
        } catch (error) {
            alert(error.message);
        }
    });

    // Get Reactions for User
    document.getElementById('getReactionsForUserButton').addEventListener('click', async () => {
        const userId = document.getElementById('getUserId').value;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/user/${userId}`);
            const result = await response.json();
            const userResultsDiv = document.getElementById('userResults');

            userResultsDiv.innerHTML = ''; // Clear previous results
            if (response.ok) {
                result.emotions.forEach(emotion => {
                    const emotionItem = document.createElement('div');
                    emotionItem.innerHTML = `Title ID: ${emotion.title_id}, Reaction: ${emotion.reaction_type}`;
                    userResultsDiv.appendChild(emotionItem);
                });
            } else {
                userResultsDiv.innerHTML = result.error || 'Error fetching reactions.';
            }
        } catch (error) {
            alert(error.message);
        }
    });

    // Update Emotion
    document.getElementById('updateEmotionButton').addEventListener('click', async () => {
        const userId = document.getElementById('updateUserId').value;
        const titleId = document.getElementById('updateTitleId').value;
        const reactionType = document.getElementById('updateReactionType').value;
        const authorId = document.getElementById('updateAuthorIdUpdate').value;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/update`, {
                ...fetchOptions,
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ user_id: userId, title_id: titleId, reaction_type: reactionType, author_id: authorId }),
            });
            const result = await response.json();
            if (!response.ok) throw new Error(result.error || 'Failed to update emotion.');

            alert(result.message || 'Emotion updated successfully!');
        } catch (error) {
            alert(error.message);
        }
    });

    // Delete Emotion
    document.getElementById('deleteEmotionButton').addEventListener('click', async () => {
        const userId = document.getElementById('deleteUserId').value;
        const titleId = document.getElementById('deleteTitleId').value;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/delete`, {
                ...fetchOptions,
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ user_id: userId, title_id: titleId }),
            });
            const result = await response.json();
            if (!response.ok) throw new Error(result.error || 'Failed to delete emotion.');

            alert(result.message || 'Emotion deleted successfully!');
        } catch (error) {
            alert(error.message);
        }
    });

     // Reset Reactions
    document.getElementById('resetReactionsButton').addEventListener('click', async () => {
        const userId = document.getElementById('resetUserId').value;

        try {
            const response = await fetch(`${pythonURI}/api/emotion/reset_reactions/${userId}`, {
                ...fetchOptions,
                method: 'DELETE',
            });

            const result = await response.json();
            if (!response.ok) throw new Error(result.error || 'Failed to reset reactions.');

            alert(result.message || 'All reactions reset successfully!');
        } catch (error) {
            alert(error.message);
        }
    });
</script>

</body>
