---
layout: page
title: Paperback Pals
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
</style>

<div id="bookContainer">
  <!-- Book content will be dynamically added here -->
</div>

<script type="module">
  import { pythonURI, fetchOptions } from "{{site.baseurl}}/assets/js/api/config.js";
  
  let currentBook = {};

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
          const coverUrl = data.cover_url || 'default-image.jpg';

          displayBookInfo(data.id, bookTitle, bookAuthor, bookGenre, bookDescription, coverUrl);
          fetchComments(); // Fetch comments from backend
        } else {
          alert('No book data found.');
        }
      })
      .catch(error => {
        console.error('Error fetching book data:', error);
        alert('Failed to fetch book information.');
      });
  }

  // Add a new comment to the backend
  function addComment() {
    const commentInput = document.getElementById('commentInput');
    const commentText = commentInput.value.trim();
    const userIdInput = document.getElementById('userIdInput');
    const userId = userIdInput.value.trim();
    const bookId = document.getElementById('bookIdInput').value.trim();

    if (commentText && userId && bookId) {
      const data = {
        comment_text: commentText,
        user_id: userId,  // Send the user ID along with the comment text
        book_id: bookId   // Send the book ID
      };

      const token = document.cookie.replace(/(?:(?:^|.*;\s*)jwtToken\s*\=\s*([^;]*).*$)|^.*$/, "$1");

      // Construct the API URL dynamically based on currentBook ID
      const apiUrl = `${pythonURI}/api/comments`;

      fetch(apiUrl, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${token}`  // Sending the token in headers
        },
        body: JSON.stringify(data)
      })
      .then(response => response.json())
      .then(data => {
        if (data.message === 'Comment added successfully') {
          fetchComments(); // Fetch updated comments
        }
      })
      .catch(error => {
        console.error('Error posting comment:', error);
        alert('Failed to add comment.');
      });
    } else {
      alert('Please enter all fields: book ID, user ID, and comment text.');
    }
  }

  // Display the book information
  function displayBookInfo(bookId, title, author, genre, description, cover_url) {
    document.getElementById('bookContainer').innerHTML = `
      <div class="book-card">
        <h3 class="book-title">Book ID: ${bookId} - ${title}</h3>
        <img src="${cover_url}" alt="Book Cover" class="book-cover" />
        <p class="book-author">by ${author}</p>
        <p class="book-genre">Genre: ${genre}</p>
        <p class="book-description">Description: ${description}</p>
        <h4 class="comments-heading">Comments:</h4>
        <div id="commentSection" class="comment-section">
          <label for="bookIdInput">Book ID:</label>
          <input type="number" id="bookIdInput" value="${bookId}" disabled />

          <label for="userIdInput">User ID:</label>
          <input type="number" id="userIdInput" placeholder="Enter your User ID" />

          <textarea id="commentInput" placeholder="Add a comment..."></textarea>
          <button onclick="addComment()" class="submit-comment">Submit</button>
          <div id="commentsList"></div>
        </div>
      </div>
    `;
  }

  // Fetch comments from the backend
  function fetchComments() {
    fetch(`${pythonURI}/api/comments?book_id=${currentBook.id}`)
      .then(response => response.json())
      .then(data => {
        if (data.comments) {
          displayComments(data.comments);
        } else {
          alert('No comments found for this book.');
        }
      })
      .catch(error => {
        console.error('Error fetching comments:', error);
        alert('Failed to fetch comments.');
      });
  }

  // Display comments fetched from the backend
  function displayComments(comments) {
    const commentsList = document.getElementById('commentsList');
    commentsList.innerHTML = ''; // Clear previous comments
    comments.forEach(comment => {
      const commentDiv = document.createElement('div');
      commentDiv.classList.add('comment-box');
      commentDiv.innerHTML = `
        <div class="comment-text">
          <strong>User ${comment.user_id}</strong><br>${comment.comment_text}
        </div>
      `;
      commentsList.appendChild(commentDiv);
    });
  }

  // Fetch book data when the page loads
  fetchRandomBook();
</script>
