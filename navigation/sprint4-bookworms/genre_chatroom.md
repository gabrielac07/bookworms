---
layout: post
title: Bookworms United
hide: true
permalink: /test4
---
<style>
.container{
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
        <div class="details" id="details">
        </div>
    </div>
</div>

<script type="module">
    const pythonURI = 'http://127.0.0.1:8887'; // Backend server URL
    import { fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';

    const userId = 1; // Hardcoded user ID (simulate logged-in user)
    let emojis = ["👍", "❤️", "😂", "🎉", "😢", "😡"]; // Predefined emojis

    async function fetchGroups() {
        try {
            const response = await fetch(`${pythonURI}/api/groups/filter`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ section_name: "Bookworms" })
            });
            if (!response.ok) {
                throw new Error('Failed to fetch groups: ' + response.statusText);
            }
            const groups = await response.json();
            const groupSelect = document.getElementById('group_id');
            groups.forEach(group => {
                const option = document.createElement('option');
                option.value = group.name;
                option.textContent = group.name;
                groupSelect.appendChild(option);
            });
        } catch (error) {
            console.error('Error fetching groups:', error);
        }
    }

    async function fetchChannels(groupName) {
        try {
            const response = await fetch(`${pythonURI}/api/channels/filter`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ group_name: groupName })
            });
            if (!response.ok) {
                throw new Error('Failed to fetch channels: ' + response.statusText);
            }
            const channels = await response.json();
            const channelSelect = document.getElementById('channel_id');
            channelSelect.innerHTML = '<option value="">Select a channel</option>';
            channels.forEach(channel => {
                const option = document.createElement('option');
                option.value = channel.id;
                option.textContent = channel.name;
                channelSelect.appendChild(option);
            });
        } catch (error) {
            console.error('Error fetching channels:', error);
        }
    }

    document.getElementById('group_id').addEventListener('change', function () {
        const groupName = this.value;
        if (groupName) {
            fetchChannels(groupName);
        } else {
            document.getElementById('channel_id').innerHTML = '<option value="">Select a channel</option>'; // Reset channels
        }
    });

    document.getElementById('selectionForm').addEventListener('submit', function(event) {
        event.preventDefault();
        const groupId = document.getElementById('group_id').value;
        const channelId = document.getElementById('channel_id').value;
        if (groupId && channelId) {
            fetchData(channelId);
        } else {
            alert('Please select both group and channel.');
        }
    });

    document.getElementById('postForm').addEventListener('submit', async function(event) {
        event.preventDefault();
        const title = document.getElementById('title').value;
        const comment = document.getElementById('comment').value;
        const channelId = document.getElementById('channel_id').value;
        const postData = {
            title: title,
            comment: comment,
            channel_id: channelId
        };
        try {
            const response = await fetch(`${pythonURI}/api/post`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(postData)
            });
            if (!response.ok) {
                throw new Error('Failed to add post: ' + response.statusText);
            }
            alert('Post added successfully!');
            document.getElementById('postForm').reset();
            fetchData(channelId);
        } catch (error) {
            console.error('Error adding post:', error);
            alert('Error adding post: ' + error.message);
        }
    });

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
            detailsDiv.innerHTML = ''; // Clear previous posts
            postData.forEach(postItem => {
                renderPost(postItem, detailsDiv);
            });
        } catch (error) {
            console.error('Error fetching posts:', error);
        }
    }

    function renderPost(post, container) {
        const postElement = document.createElement('div');
        postElement.className = 'post-item';
        postElement.innerHTML = `
            <h3>${post.title}</h3>
            <p><strong>Channel:</strong> ${post.channel_name}</p>
            <p><strong>User:</strong> ${post.user_name}</p>
            <p>${post.comment}</p>
            <button onclick="showReactionOptions(${post.id})">React</button>
            <div class="reactions" id="reactions-${post.id}"></div>
        `;
        container.appendChild(postElement);
        fetchReactions(post.id);
    }

    function showReactionOptions(postId) {
        const reactionsDiv = document.querySelector(`#reactions-${postId}`);
        reactionsDiv.innerHTML = '';
        emojis.forEach(emoji => {
            const button = document.createElement('button');
            button.textContent = emoji;
            button.onclick = () => addOrUpdateReaction(postId, emoji);
            reactionsDiv.appendChild(button);
        });
    }

    async function addOrUpdateReaction(postId, emoji) {
        try {
            const response = await fetch(`${pythonURI}/api/reaction`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ user_id: userId, post_id: postId, reaction_type: emoji })
            });
            if (!response.ok) {
                throw new Error('Failed to add reaction: ' + response.statusText);
            }
            const result = await response.json();
            alert('Reaction added successfully!');
            fetchReactions(postId);
        } catch (error) {
            console.error('Error adding reaction:', error);
            alert('Error adding reaction: ' + error.message);
        }
    }

    async function fetchReactions(postId) {
        try {
            const response = await fetch(`${pythonURI}/api/reactions/filter`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ post_id: postId })
            });
            if (!response.ok) {
                throw new Error('Failed to fetch reactions: ' + response.statusText);
            }
            const reactions = await response.json();
            // Optionally display the reactions count or details
            console.log(`Post ${postId} has ${reactions.length} reactions.`);
        } catch (error) {
            console.error('Error fetching reactions:', error);
        }
    }

    // Fetch groups when the page loads
    fetchGroups();
</script>


- ^^^ this one looks good but button doesnt work
---
layout: post
title: Bookworms United
hide: true
permalink: /test4
---
<style>
.container{
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
        <div class="details" id="details">
        </div>
    </div>
</div>
<script type="module">
    // Import server URI and standard fetch options
    import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';
    /**
     * Fetch groups for dropdown selection
     * User picks from dropdown
     */
    async function fetchGroups() {
        try {
            const response = await fetch(`${pythonURI}/api/groups/filter`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ section_name: "Bookworms" }) // Adjust the section name if needed
            });
            if (!response.ok) {
                throw new Error('Failed to fetch groups: ' + response.statusText);
            }
            const groups = await response.json();
            const groupSelect = document.getElementById('group_id');
            groups.forEach(group => {
                const option = document.createElement('option');
                option.value = group.name; // Use group name for payload
                option.textContent = group.name;
                groupSelect.appendChild(option);
            });
        } catch (error) {
            console.error('Error fetching groups:', error);
        }
    }
    document.addEventListener('DOMContentLoaded', function() {
        // Add event listener to the react button
        document.getElementById('reactButton').addEventListener('click', function() {
            const postId = this.getAttribute('data-post-id');
            reactToPost(postId);
        });
    });
    /**
     * Fetch channels based on selected group
     * User picks from dropdown
     */
    async function fetchChannels(groupName) {
        try {
            const response = await fetch(`${pythonURI}/api/channels/filter`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ group_name: groupName }) // Pass selected group name here
            });
            if (!response.ok) {
                throw new Error('Failed to fetch channels: ' + response.statusText);
            }
            const channels = await response.json();
            const channelSelect = document.getElementById('channel_id');
            channelSelect.innerHTML = '<option value="">Select a channel</option>'; // Reset channels
            channels.forEach(channel => {
                const option = document.createElement('option');
                option.value = channel.id;
                option.textContent = channel.name;
                channelSelect.appendChild(option);
            });
        } catch (error) {
            console.error('Error fetching channels:', error);
        }
    }
    // Handle Post Reaction
    async function reactToPost(postId) {
        const reactionType = prompt("Enter your reaction emoji (e.g., 👍, ❤️):");
        if (!reactionType) return;
        try {
            const response = await fetch(`${pythonURI}/api/reaction`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ user_id: 1, post_id: postId, reaction_type: reactionType }) // Assuming user_id is 1 for simplicity
            });
            if (!response.ok) {
                throw new Error('Failed to add reaction: ' + response.statusText);
            }
            const result = await response.json();
            alert('Reaction added successfully!');
            // Update the post with the new reaction
            updatePostReaction(postId, reactionType);
        } catch (error) {
            console.error('Error:', error);
            alert('An error occurred while adding the reaction.');
        }
    }
    // Update the post with the new reaction
    function updatePostReaction(postId, reaction) {
        const postElement = document.querySelector(`[data-post-id="${postId}"]`);
        if (postElement) {
            const reactionElement = postElement.querySelector('.reaction');
            if (reactionElement) {
                reactionElement.textContent = reaction;
            } else {
                const newReactionElement = document.createElement('div');
                newReactionElement.classList.add('reaction');
                newReactionElement.textContent = reaction;
                postElement.appendChild(newReactionElement);
            }
        }
    }
    // Fetch posts when the page loads
    document.getElementById('selectionForm').addEventListener('submit', function(event) {
        event.preventDefault();
        const channelId = document.getElementById('channel_id').value;
        fetchData(channelId);
    });
    /**
     * Handle group selection change
     * Channel Dropdown refresh to match group_id change
     */
    document.getElementById('group_id').addEventListener('change', function() {
        const groupName = this.value;
        if (groupName) {
            fetchChannels(groupName);  // Fetch channels for the selected group
        } else {
            document.getElementById('channel_id').innerHTML = '<option value="">Select a channel</option>'; // Reset channels
        }
    });
    /**
     * Handle form submission for selection
     * Select Button: Computer fetches and displays posts
     */
    document.getElementById('selectionForm').addEventListener('submit', function(event) {
        event.preventDefault();
        const groupId = document.getElementById('group_id').value;
        const channelId = document.getElementById('channel_id').value;
        if (groupId && channelId) {
            fetchData(channelId);
        } else {
            alert('Please select both group and channel.');
        }
    });
    /**
     * Handle form submission for adding a post
     * Add Form Button: Computer handles form submission with request
     */
    document.getElementById('postForm').addEventListener('submit', async function(event) {
        event.preventDefault();
        // Extract data from form
        const title = document.getElementById('title').value;
        const comment = document.getElementById('comment').value;
        const channelId = document.getElementById('channel_id').value;
        // Create API payload
        const postData = {
            title: title,
            comment: comment,
            channel_id: channelId
        };
        // Trap errors
        try {
            // Send POST request to backend, purpose is to write to database
            const response = await fetch(`${pythonURI}/api/post`, {
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(postData)
            });
            if (!response.ok) {
                throw new Error('Failed to add post: ' + response.statusText);
            }
            // Successful post
            const result = await response.json();
            alert('Post added successfully!');
            document.getElementById('postForm').reset();
            fetchData(channelId);
        } catch (error) {
            // Present alert on error from backend
            console.error('Error adding post:', error);
            alert('Error adding post: ' + error.message);
        }
    });
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
        // Parse the JSON data
        const postData = await response.json();
        // Extract posts count
        const postCount = postData.length || 0;
        // Update the HTML elements with the data
        document.getElementById('count').innerHTML = `<h2>Count ${postCount}</h2>`;
        // Get the details div
        const detailsDiv = document.getElementById('details');
        detailsDiv.innerHTML = ''; // Clear previous posts
        // Iterate over the postData and create HTML elements for each item
        postData.forEach(postItem => {
            const postElement = document.createElement('div');
            postElement.className = 'post-item';
            postElement.innerHTML = `
                <h3>${postItem.title}</h3>
                <p><strong>Channel:</strong> ${postItem.channel_name}</p>
                <p><strong>User:</strong> ${postItem.user_name}</p>
                <p>${postItem.comment}</p>
                <button class="react-button" data-post-id="${postItem.id}">React</button>
            `;
            detailsDiv.appendChild(postElement);
        });
        // Attach event listeners to all react buttons
        const reactButtons = document.querySelectorAll('.react-button');
        reactButtons.forEach(button => {
            button.addEventListener('click', function () {
                const postId = this.getAttribute('data-post-id');
                reactToPost(postId);
            });
        });
    } catch (error) {
        console.error('Error fetching posts:', error);
    }
}
    // Fetch groups when the page loads
    fetchGroups();
</script>