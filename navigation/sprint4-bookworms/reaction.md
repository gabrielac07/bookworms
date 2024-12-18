<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reactions and Emojis</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        .message {
            margin: 10px 0;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        .reactions {
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>Reactions and Emojis</h1>
    <!-- Example Message -->
    <div id="messages">
        <div class="message" id="message-123">
            <p>Message ID: 123</p>
            <div class="reactions" id="reactions-123"></div>
            <button onclick="addReaction('123', '👍')">👍</button>
            <button onclick="addReaction('123', '❤️')">❤️</button>
            <button onclick="addReaction('123', '😂')">😂</button>
            <button onclick="resetReactions('123')">Reset Reactions</button>
        </div>
    </div>
    <h2>Available Emojis</h2>
    <div id="emoji-list"></div>
    <input type="text" id="new-emoji" placeholder="Add a custom emoji" />
    <button onclick="addEmoji()">Add Emoji</button>
    <script>
        const BASE_URL = 'http://127.0.0.1:5000';
        // Add a reaction
        function addReaction(messageId, reaction) {
            fetch(`${BASE_URL}/add_reaction`, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ message_id: messageId, reaction }),
            })
                .then(response => response.json())
                .then(data => {
                    console.log('Reaction added:', data);
                    displayReactions(messageId);
                })
                .catch(error => console.error('Error adding reaction:', error));
        }
        // Get reactions for a message
        function displayReactions(messageId) {
            fetch(`${BASE_URL}/get_reactions/${messageId}`)
                .then(response => response.json())
                .then(data => {
                    const reactionsDiv = document.getElementById(`reactions-${messageId}`);
                    reactionsDiv.innerHTML = Object.entries(data.reactions)
                        .map(([emoji, count]) => `${emoji}: ${count}`)
                        .join(', ');
                })
                .catch(error => console.error('Error fetching reactions:', error));
        }
        // Get available emojis
        function getEmojis() {
            fetch(`${BASE_URL}/get_emojis`)
                .then(response => response.json())
                .then(data => {
                    const emojiList = document.getElementById('emoji-list');
                    emojiList.innerHTML = data.emojis.join(' ');
                })
                .catch(error => console.error('Error fetching emojis:', error));
        }
        // Add a custom emoji
        function addEmoji() {
            const newEmoji = document.getElementById('new-emoji').value;
            if (!newEmoji) return alert('Please enter an emoji!');
            fetch(`${BASE_URL}/add_emoji`, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ emoji: newEmoji }),
            })
                .then(response => response.json())
                .then(data => {
                    console.log('Emoji added:', data);
                    getEmojis();
                })
                .catch(error => console.error('Error adding emoji:', error));
        }
        // Reset reactions for a message
        function resetReactions(messageId) {
            fetch(`${BASE_URL}/reset_reactions/${messageId}`, {
                method: 'DELETE',
            })
                .then(response => response.json())
                .then(data => {
                    console.log('Reactions reset:', data);
                    displayReactions(messageId);
                })
                .catch(error => console.error('Error resetting reactions:', error));
        }
        // Initialize
        getEmojis();
        displayReactions('123');
    </script>
</body>
</html>
