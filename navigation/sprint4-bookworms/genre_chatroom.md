---
layout: post
title: Bookworms United
hide: true
permalink: /test4
---
<style>
.container {
    background: #F9E4BC; 
    color: #562E19;
    padding-bottom: 10px; 
}
.post-item {
    border: 1px solid #ccc;
    padding: 16px;
    margin: 16px 0;
    border-radius: 8px;
    background-color: #f9f9f9;
}

.post-item h3 {
    margin-top: 0;
}

.post-item button {
    margin-top: 8px;
}
</style>

<div class="container">
    <div class="form-container">
        <h2>Select a genre</h2>
        <form id="selectionForm">
            <label for="group_id">Group:</label>
            <select id="group_id" name="group_id" required>
                <option value="">Select a group</option>
            </select>
            <label for="channel_id">Channel:</label>
            <select id="channel_id" name="channel_id" required>
                <option value="">Select a channel</option>
            </select>
            <button type="submit">Select</button>
        </form>
    </div>
</div>
<div class="container">
    <div class="form-container">
        <h2>Add New Post</h2>
        <form id="postForm">
            <label for="title">Title:</label>
            <input type="text" id="title" name="title" required>
            <label for="comment">Comment:</label>
            <textarea id="comment" name="comment" required></textarea>
            <button type="submit">Add Post</button>
        </form>
    </div>
</div>
<div class="container">
    <div id="data" class="data">
        <div class="left-side">
            <p id="count"></p>
        </div>
        <div class="details" id="details"></div>
    </div>
</div>
<script type="module">
    // Import server URI and standard fetch options
    import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';
    async function fetchData(channelId) {
        try {
            const response = await fetch(`${pythonURI}/api/posts/filter`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ channel_id: channelId })
            });
            if (!response.ok) {
                throw new Error('Failed to fetch posts: ' + response.statusText);
            }
            const postData = await response.json();
            const postCount = postData.length || 0;
            document.getElementById('count').innerHTML = `<h2>Count ${postCount}</h2>`;
            const detailsDiv = document.getElementById('details');
            detailsDiv.innerHTML = '';
            postData.forEach(postItem => {
                const postElement = document.createElement('div');
                postElement.className = 'post-item';
                postElement.innerHTML = `
                    <h3>${postItem.title}</h3>
                    <p><strong>Channel:</strong> ${postItem.channel_name}</p>
                    <p><strong>User:</strong> ${postItem.user_name}</p>
                    <p>${postItem.comment}</p>
                    <button class="react-button" data-post-id="${postItem.id}">React</button>
                    <div id="reactions-${postItem.id}"></div> <!-- Div for displaying reactions -->
                `;
                detailsDiv.appendChild(postElement);
                fetchReactions(postItem.id); // Fetch reactions for each post
            });
        } catch (error) {
            console.error('Error fetching posts:', error);
        }
    }
    // Event delegation for reaction buttons
    document.getElementById('details').addEventListener('click', function(event) {
        if (event.target.classList.contains('react-button')) {
            const postId = event.target.getAttribute('data-post-id');
            reactToPost(postId);
        }
    });
    async function reactToPost(postId) {
        console.log(`Reacting to post ID: ${postId}`); // Debugging statement
        const userId = prompt("Enter your User ID:");
        const reactionType = prompt("Enter your reaction (e.g., 👍, ❤️, 😂):");
        if (userId && reactionType) {
            const reactionData = {
                user_id: userId,
                post_id: postId,
                reaction_type: reactionType
            };
            try {
                const response = await fetch(`${pythonURI}/api/reaction`, {
                    ...fetchOptions,
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(reactionData)
                });
                if (!response.ok) {
                    throw new Error('Failed to add reaction: ' + response.statusText);
                }
                alert('Reaction added successfully!');
                fetchReactions(postId); // Refresh reactions after adding
            } catch (error) {
                console.error('Error adding reaction:', error);
                alert('Error adding reaction: ' + error.message);
            }
        }
    }
</script>
