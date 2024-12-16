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

<script>
  const username = prompt("Please enter your username:") || "Anonymous";

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
  fetch(`http://172.19.255.157:8887/api/random_book`)
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
      <h4 class="comments-heading">Comments:</h4>
      <div id="commentSection" class="comment-section">
        <textarea id="commentInput" placeholder="Add a comment..."></textarea>
        <button onclick="addComment()" class="submit-comment">Submit</button>
        <div id="commentsList"></div>
      </div>
    </div>
  `;
}

    displayComments();
  }

  // Fetch book data when the page loads
  fetchRandomBook();

  function displayComments() {
    const commentsList = document.getElementById('commentsList');
    commentsList.innerHTML = '';
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
