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
    background-color: #D4AFB9;
    color: #BC4749;
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
    color: #BC4749;
  }
  .book-author {
    font-size: 18px;
    color: #BC4749;
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
    background-color: #BC4749;
    color: white;
    padding: 15px;
    border-radius: 5px;
    text-align: left;
    margin-top: 15px;
  }
  .submit-comment {
    display: block;
    margin-top: 10px;
    background-color: #C45A5C;
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
    background-color: #873132;
    border-radius: 5px;
  }
  .comment-text {
    color: white;
  }
  .search-bar {
    width: 100%;
    max-width: 500px;
    margin: 20px auto;
    padding: 10px;
    font-size: 16px;
    border-radius: 5px;
    border: 1px solid #C45A5C;
    color: #BC4749;
  }
</style>

<div class="search-container">
  <input type="number" id="bookIdSearch" class="search-bar" placeholder="Enter Book ID to Search" />
  <button id="searchButton" class="submit-comment">Search</button>
</div>

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

  // Fetch book by ID
  function fetchBookById(bookId) {
    fetch(`${pythonURI}/api/books/${bookId}`)
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
          alert('Book not found.');
        }
      })
      .catch(error => {
        console.error('Error fetching book data:', error);
        alert('Failed to fetch book information.');
      });
  }

  // Display the book information
  function displayBookInfo(id, title, author, genre, description, cover_url) {
    document.getElementById('bookContainer').innerHTML = `
      <div class="book-card">
        <h3 class="book-title">Book ID: ${id} - ${title}</h3>
        <img src="${cover_url}" alt="Book Cover" class="book-cover" />
        <p class="book-author">by ${author}</p>
        <p class="book-genre">Genre: ${genre}</p>
        <p class="book-description">Description: ${description}</p>
        <h4 class="comments-heading">Comments:</h4>
        <div id="commentSection" class="comment-section">
          <label for="bookIdInput">Book ID:</label>
          <input type="number" id="bookIdInput" value="${id}" disabled />

          <label for="userIdInput">User ID:</label>
          <input type="number" id="userIdInput" placeholder="Enter your User ID" />

          <textarea id="commentInput" placeholder="Add a comment..."></textarea>
          <button id="submitCommentBtn" class="submit-comment">Submit</button>
          <div id="commentsList"></div>
        </div>
      </div>
    `;

    // Add event listener for the submit button
    const submitButton = document.getElementById('submitCommentBtn');
    submitButton.addEventListener('click', addComment);
  }

  // Add comment function
  function addComment() {
    const commentInput = document.getElementById('commentInput');
    const commentText = commentInput.value.trim();
    const userId = document.getElementById('userIdInput').value.trim();

    if (commentText === '') {
      alert('Comment cannot be empty.');
      return;
    }

    if (!userId) {
      alert('Please enter a valid User ID.');
      return;
    }

    const commentData = {
      book_id: currentBook.id,
      user_id: userId,
      comment_text: commentText
    };

    // Debug: Log the comment data to verify it's correct
    console.log('Sending comment data:', commentData);

    fetch(`${pythonURI}/api/comments`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(commentData)
    })
    .then(response => {
      if (!response.ok) {
        throw new Error('Network response was not ok');
      }
      return response.json();
    })
    .then(data => {
      console.log('Response data:', data);
      if (data.success) {
        // Optionally append the new comment immediately without waiting for fetchComments()
        const commentList = document.getElementById('commentsList');
        
        const newComment = document.createElement('div');
        newComment.classList.add('comment');
        newComment.innerHTML = `
          <p><strong>User ${userId}:</strong> ${commentText}</p>
        `;
        commentList.appendChild(newComment); // Add the new comment to the list

        // Clear the input field
        commentInput.value = '';
      } else {
        alert('Successfully added comment! Refresh to check');
      }
    })
    .catch(error => {
      console.error('Error adding comment:', error);
      alert('Failed to add comment.');
    });
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

  // Search for a book by ID
  function searchBookById() {
    const bookId = document.getElementById('bookIdSearch').value;
    if (bookId) {
      fetchBookById(bookId);
    } else {
      alert('Please enter a valid Book ID.');
    }
  }

  // Add event listener to search button
  document.getElementById('searchButton').addEventListener('click', searchBookById);

  // Fetch random book when page loads
  fetchRandomBook();
</script>
