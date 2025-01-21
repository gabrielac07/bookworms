---
layout: page
title: Reader Report
permalink: /bookrates/
---
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Book Reviews</title>
<style>
  /* Styling for elements */
  .book-card {
    background-color: #072536;
    color: #AFC0D5;
    padding: 20px;
    max-width: 700px;
    margin: 20px auto;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    text-align: center;
  }
  .book-title {
    font-size: 24px;
    margin-bottom: 10px;
    color: #FFFFFF;
  }
  .book-author {
    font-size: 18px;
    color: #D1E1F2;
  }
  .book-cover {
    width: 400px;
    height: 600px;
    border-radius: 5px;
    margin: 20px auto;
  }
  .heart-button {
    background: none;
    border: none;
    font-size: 24px;
    color: #ff0077;
    cursor: pointer;
  }
  .rating-stars span {
    font-size: 30px;
    cursor: pointer;
    color: #ccc;
  }
  .comments-heading {
    color: #fff;
    margin-top: 20px;
  }
  .comment-section {
    background-color: #072536;
    color: white;
    padding: 15px;
    border-radius: 5px;
    text-align: left;
    margin-top: 15px;
  }
  .submit-comment {
    display: block;
    margin-top: 10px;
    background-color: #33ccff;
    border: none;
    color: white;
    padding: 8px 12px;
    border-radius: 5px;
    cursor: pointer;
    text-align: left;
  }
  .comment-box {
    border-bottom: 1px solid #cce7ff;
    padding: 10px;
    margin-top: 10px;
    background-color: #0B3954;
    border-radius: 5px;
  }
  .comment-text {
    color: white;
  }
  .reply-button {
    background: none;
    color: #cce7ff;
    border: none;
    cursor: pointer;
    font-size: 14px;
    padding: 0;
    margin-top: 5px;
    text-align: left;
  }
  .reply-textarea {
    width: 100%;
    padding: 5px;
    margin-top: 5px;
    border: none;
    border-radius: 5px;
    font-size: 14px;
    color: #333;
  }
  .submit-reply {
    background-color: #66b2ff;
    border: none;
    padding: 5px 10px;
    color: white;
    border-radius: 5px;
    margin-top: 5px;
    cursor: pointer;
    text-align: left;
  }
  .replies {
    margin-top: 10px;
    padding-left: 10px;
  }
  .reply-box {
    background-color: #10567E;
    padding: 5px;
    border-radius: 5px;
    color: #cce7ff;
    margin-top: 5px;
    text-align: left;
  }
</style>

<div id="bookContainer">
  <!-- Book content will be dynamically added here -->
</div>

<div class="container">
    <div class="form-container">
        <form id="selectionForm">
            <label for="group_id">Genre:</label>
            <select id="group_id" name="group_id" required>
                <option value="">Select genre</option>
            </select>
            <label for="channel_id">Title:</label>
            <select id="channel_id" name="channel_id" required>
                <option value="">Select book title</option>
            </select>
            <button type="submit">Select</button>
        </form>
    </div>
</div>
<div class="container">
    <div class="form-container">
        <h2>Add New Comment</h2>
        <form id="postForm">
            <label for="title">Comment Title:</label>
            <input type="text" id="title" name="title" required>
            <br>
            <label for="comment">Comment:</label>
            <textarea id="comment" name="comment" required></textarea>
            <button type="submit">Add Comment</button>
        </form>
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
    /**
     * Fetch posts based on selected channel
     * Handle response: Fetch and display posts
     */
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
                `;
                detailsDiv.appendChild(postElement);
            });
        } catch (error) {
            console.error('Error fetching data:', error);
        }
    }
    // Fetch groups when the page loads
    fetchGroups();
</script>


<script type="module">
  import { pythonURI, fetchOptions } from "{{site.baseurl}}/assets/js/api/config.js";
  
  const bookRatings = {
    "Harry Potter and the Sorcerer's Stone": 4.8,
    "Percy Jackson & the Olympians: The Lightning Thief": 4.3,
    "The Hunger Games": 4.2,
    "Divergent": 4.1,
    "Red Queen": 4.0,
    "A Good Girl's Guide to Murder": 4.5
  };

  let currentBook = {};
  let liked = false;
  let currentLikes = 0;

  let comments = JSON.parse(localStorage.getItem('comments')) || {};
  let likesCount = JSON.parse(localStorage.getItem('likes')) || {};

// Fetch random book from backend (Flask API)
function fetchRandomBook() {
  fetch(`${pythonURI}/api/random_book`)
    .then(response => response.json())
    .then(data => {
      if (data && data.title) {
        currentBook = data;
        const bookTitle = data.title;
        const bookAuthor = data.author || 'Unknown Author';
        const bookGenre = data.genre || 'Unknown Genre';
        const bookDescription = data.description || 'No description available';
        const coverUrl = data.image_cover || 'default-image.jpg';

        displayBookInfo(bookTitle, bookAuthor, bookGenre, bookDescription, coverUrl);
      } else {
        alert('No book data found.');
      }
    })
    .catch(error => {
      console.error('Error fetching book data:', error);
      alert('Failed to fetch book information.');
    });
}

// Display the book information
function displayBookInfo(title, author, genre, description, coverUrl) {
  document.getElementById('bookContainer').innerHTML = `
    <div class="book-card">
      <h3 class="book-title">${title}</h3>
      <img src="${coverUrl}" alt="Book Cover" class="book-cover" />
      <p class="book-author">by ${author}</p>
      <p class="book-genre">Genre: ${genre}</p>
      <p class="book-description">Description: ${description}</p>
      <div id="rating" class="rating-stars">
        <span onclick="rateBook(1)">★</span>
        <span onclick="rateBook(2)">★</span>
        <span onclick="rateBook(3)">★</span>
        <span onclick="rateBook(4)">★</span>
        <span onclick="rateBook(5)">★</span>
      </div>
      <p>Average Rating: ${bookRatings[title] || 'No Rating'} ★</p>
      <button onclick="toggleLike()" id="likeButton" class="heart-button">
        <span id="heart">♡</span> Like (${currentLikes})
      </button>
    </div>
  `;
}

    displayComments();

  // Fetch book data when the page loads
  fetchRandomBook();

  function displayComments() {
    const commentsList = document.getElementById('commentsList');
    const bookComments = comments[currentBook.title] || [];
    bookComments.forEach((comment, index) => {
      const commentDiv = document.createElement('div');
      commentDiv.classList.add('comment-box');
      commentDiv.innerHTML = `
        <div class="comment-text">
          <strong>${comment.username}</strong><br>${comment.text}
        </div>
        <button onclick="showReplyInput(${index})" class="reply-button">Reply</button>
        <div id="replyInput-${index}" style="display: none;">
          <textarea placeholder="Add a reply..." class="reply-textarea"></textarea>
          <button onclick="addReply(${index})" class="submit-reply">Submit Reply</button>
        </div>
        <div class="replies">
          ${comment.replies.map(reply => ` 
            <div class="reply-box">
              <strong>${reply.username}</strong>: ${reply.text}
            </div>`).join('')}
        </div>
      `;
      commentsList.appendChild(commentDiv);
    });
  }

  function saveComments() {
    localStorage.setItem('comments', JSON.stringify(comments));
  }

  function addComment() {
    const commentInput = document.getElementById('commentInput');
    const commentText = commentInput.value.trim();
    if (commentText) {
      const comment = { username, text: commentText, replies: [] };
      if (!comments[currentBook.title]) {
        comments[currentBook.title] = [];
      }
      comments[currentBook.title].push(comment);
      commentInput.value = '';
      saveComments();
      displayComments();
    }
  }

  function addReply(commentIndex) {
    const replyInput = document.getElementById(`replyInput-${commentIndex}`).querySelector('textarea');
    const replyText = replyInput.value.trim();
    if (replyText) {
      comments[currentBook.title][commentIndex].replies.push({ username, text: replyText });
      replyInput.value = '';
      saveComments();
      displayComments();
    }
  }

  function showReplyInput(commentIndex) {
    const replyInput = document.getElementById(`replyInput-${commentIndex}`);
    replyInput.style.display = replyInput.style.display === 'none' ? 'block' : 'none';
  }

  function rateBook(rating) {
    const bookTitle = currentBook.title;
    // Update the book rating using a more accurate average calculation
    bookRatings[bookTitle] = (bookRatings[bookTitle] * currentLikes + rating) / (currentLikes + 1);
    currentLikes++;
    document.querySelectorAll('.rating-stars span').forEach((star, index) => {
      star.style.color = index < rating ? 'yellow' : 'gray';
    });
    saveComments();
  }

  function toggleLike() {
    if (liked) {
      liked = false;
      currentLikes--;
      document.getElementById('heart').textContent = '♡';
    } else {
      liked = true;
      currentLikes++;
      document.getElementById('heart').textContent = '❤️';
    }
    document.getElementById('likeButton').textContent = `Like (${currentLikes})`;
    likesCount[currentBook.title] = currentLikes;
    localStorage.setItem('likes', JSON.stringify(likesCount));
  }
</script>
