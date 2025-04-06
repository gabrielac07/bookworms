---
layout: page
title: Tailored Tales
permalink: /bookadd/
---
<style>
    .container {
        max-width: 600px;
        margin: 50px auto;
        padding: 20px;
        background-color: #E8C5A4;
        border-radius: 8px;
    }
    

    h1 {
        background: #a57e5a;
        padding: 50px;
        font-size: 2em;
        text-align: center;
        color: #4C4C4C;
    }

    .container > h2 {
        margin: 20px 0 10px;
        font-size: 1.5em;
        color: #4C4C4C !important;
    }
    .container2 {
        max-width: 800px;
        margin: 50px auto;
        padding: 20px;
        background-color: #E8C5A4;
        border-radius: 8px;
    }
    .container2 > h2 {
        margin: 20px 0 10px;
        font-size: 1.5em;
        color: #4C4C4C !important;
    }

    h3 {
        color: #E8C5A4;

    }

    label {
        display: block;
        margin-bottom: 5px;
        font-weight: bold;
        color: black;
        color: #0d160b;
    }

    input, textarea, select {
        width: 100%;
        padding: 10px;
        margin-bottom: 20px;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 16px;
    }
    
    select {
        background-color: #a57e5a;

    }

    button {
        padding: 10px 15px;
        font-size: 18px;
        margin: 10px 0;
        color: white;
        background-color: #a57e5a;
        border-radius: 4px;
        cursor: pointer;
        border: 1px solid #500A0A;
    }

    button:hover {
        background-color: #500A0A;
        transition: 0.3s;
    }

    .start_over {
        margin-top: 20px;
        color: white;
        border: none;
        padding: 10px 15px;
        cursor: pointer;
        border-radius: 4px;
        border: 1px solid #500A0A;
    }

    .start_over:hover {
        background-color: #500A0A;
        transition: 0.3s;
    }

    /* Styling for Book List */
    #book-list-content {
        display: flex;
        flex-wrap: wrap;
        gap: 20px;
        justify-content: center;
    }

    .book {
        background-color: #a57e5a;
        padding: 15px;
        border: 1px solid #ccc;
        border-radius: 8px;
        max-width: 200px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        text-align: center;
        color: #faebd8;
    }

    .book h2 {
        color: #E8C5A4;
        font-size: 1.2em;
        margin-bottom: 10px;
    }

    .book p {
        color: #E8C5A4;
        margin: 5px 0;
        font-size: 0.9em;
    }

    .book img {
        max-width: 100px;
        height: auto;
        margin: 10px auto;
        display: block;
        border-radius: 4px;
    }

    .book-options {
        display: flex;
        flex-wrap: wrap;
        gap: 0.5em;
    }

    .book-options button {
        width: 45%;
        font-size: 11px;
    }


    /*expand button code*/
    #book-list-content.collapsed .book {
    display: none;
    }

    #book-list-content.collapsed .book:nth-child(-n+3) {
        display: block;
    }

    #book-list-content {
        overflow: hidden;
        transition: max-height 0.5s ease;
    }

    #book-list-content.collapsed {
        max-height: 680px; 
    }

    #toggle-books {
        display: block;
        margin: 20px auto;
        padding: 10px 20px;
        background-color: #a57e5a;
        color: white;
        border: 1px solid #500A0A;
        border-radius: 4px;
        cursor: pointer;
    }

    #toggle-books:hover {
        background-color: #500A0A;
        transition: 0.3s;
    }

    .admin-button {
    display: none;
    }
</style>

<div class="container">
    <h1>Suggest your favorite books here!</h1>
    <form id="book-form">
        <div id="books-container">
            <div class="book-entry">
                <h2>Book Title</h2>
                <label for="title">Title</label>
                <input type="text" class="title" placeholder="Enter book title" required>
                <h2>Author</h2>
                <label for="author">Author</label>
                <input type="text" class="author" placeholder="Enter author's name" required>
                <h2>Genre</h2>
                <label for="genre">Genre</label>
                <select class="genre" required>
                    <option value="">Select Genre</option>
                    <option value="Classics">Classics</option>
                    <option value="Fantasy">Fantasy</option>
                    <option value="Nonfiction">Nonfiction</option>
                    <option value="Historical Fiction">Historical Fiction</option>
                    <option value="Suspense/Thriller">Suspense/Thriller</option>
                    <option value="Romance">Romance</option>
                    <option value="Dystopian">Dystopian</option>
                    <option value="Mystery">Mystery</option>
                </select>
                <h2>Description</h2>
                <label for="description">Description</label>
                <textarea class="description" rows="5" placeholder="Enter a brief description" required></textarea>
                <h2>Cover Image</h2>
                <label for="cover_url">Cover Image URL</label>
                <input type="url" class="cover_url" placeholder="Enter image URL" required>
                <button type="button" class="remove-book">Remove Book</button>
            </div>
        </div>
        <button type="button" id="add-book">Add Another Book</button>
        <button type="submit">Submit Books</button>
        <button type="reset" class="start_over">Start Over</button>
    </form>
</div>

<div id="book-list" class="container2">
    <h1>Your Suggested Books</h1><br>
    <label for="sort-books">Sort Books:</label>
    <select id="sort-books">
        <option value="none">No Sort (Oldest > Newest)</option>
        <option value="alphabetical">Alphabetically</option>
    </select>
    <br><br>
    <div id="accepted-books-container">
    <h2>Accepted Books:</h2>
    <p id="accepted-books-list" style="color: #a57e5a;">None yet.</p></div>
    <div id="rejected-books-container">
    <h3>Rejected Books:</h3>
    <p id="rejected-books-list" style="color: #a57e5a;">None yet.</p></div><br>
    <div id="book-list-content" class="collapsed">
        <p style="color: #a57e5a">No books added yet. Fill out the form above to start adding your favorite books!</p>
    </div>
    <button id="toggle-books">Show More</button>
</div>


<script type="module">
    import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';

    const toggleButton = document.getElementById('toggle-books');
    const bookList = document.getElementById('book-list-content');

    toggleButton.addEventListener('click', () => {
        bookList.classList.toggle('collapsed');
        bookList.classList.toggle('expanded');
        
        toggleButton.textContent = 
            bookList.classList.contains('collapsed') ? 'Show More' : 'Show Less';
    });


    document.getElementById('add-book').addEventListener('click', () => {
        const container = document.getElementById('books-container');
        const newEntry = document.createElement('div');
        newEntry.classList.add('book-entry');
        newEntry.innerHTML = `
            <h2>Book Title</h2>
            <label>Title</label>
            <input type="text" class="title" placeholder="Enter book title" required>

            <h2>Author</h2>
            <label>Author</label>
            <input type="text" class="author" placeholder="Enter author's name" required>

            <h2>Genre</h2>
            <label>Genre</label>
            <select class="genre" required>
                <option value="">Select Genre</option>
                <option value="Classics">Classics</option>
                <option value="Fantasy">Fantasy</option>
                <option value="Nonfiction">Nonfiction</option>
                <option value="Historical Fiction">Historical Fiction</option>
                <option value="Suspense/Thriller">Suspense/Thriller</option>
                <option value="Romance">Romance</option>
                <option value="Dystopian">Dystopian</option>
                <option value="Mystery">Mystery</option>
            </select>

            <h2>Description</h2>
            <label>Description</label>
            <textarea class="description" rows="5" placeholder="Enter a brief description" required></textarea>

            <h2>Cover Image</h2>
            <label>Cover Image URL</label>
            <input type="url" class="cover_url" placeholder="Enter image URL" required>

            <button type="button" class="remove-book">Remove Book</button>
        `;

        // Add remove button handler
        newEntry.querySelector('.remove-book').addEventListener('click', () => {
            container.removeChild(newEntry);
        });

        container.appendChild(newEntry);
    });

    // Handle form submission for bulk data
    document.getElementById('book-form').addEventListener('submit', async (event) => {
        event.preventDefault();

        const books = Array.from(document.querySelectorAll('.book-entry')).map(entry => ({
            title: entry.querySelector('.title').value,
            author: entry.querySelector('.author').value,
            genre: entry.querySelector('.genre').value,
            description: entry.querySelector('.description').value,
            cover_url: entry.querySelector('.cover_url').value
        }));

        if (books.length === 0) {
            alert('Please add at least one book.');
            return;
        }

        try {
            const response = await fetch(`${pythonURI}/api/suggest/bulk`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(books)
            });

            if (!response.ok) {
                throw new Error(`Failed to add books: ${response.statusText}`);
            }

            const result = await response.json();
            console.log('Bulk submission result:', result);

            let successCount = 0;
            let errorCount = 0;

            result.forEach(res => {
                if (res.error) {
                    console.error(`Failed to add book "${res.title}": ${res.message}`);
                    errorCount++;
                } else {
                    console.log(`Book "${res.title}" added successfully.`);
                    successCount++;
                }
            });


            // Reset form if successful
            if (successCount > 0) {
                document.getElementById('book-form').reset();
                document.getElementById('books-container').innerHTML = ''; // Clear all entries
            }
        } catch (error) {
            console.error('Error adding books:', error);
            alert(`Error: ${error.message}`);
        }
    });

    // Reset handler (optional)
    document.querySelector('.start_over').addEventListener('click', () => {
        document.getElementById('books-container').innerHTML = ''; // Clear all books
    });

    async function fetchRandomBook() {
        try {
            const response = await fetch(`${pythonURI}/api/suggest/random`);  // Use /api/suggest/random for GET
            if (!response.ok) {
                throw new Error('Failed to fetch random book: ' + response.statusText);
            }
            const book = await response.json();
        } catch (error) {
            console.error('Error fetching random book:', error);
        }
    }

    fetchRandomBook();

    async function deleteBook(title) {
        if (confirm(`Are you sure you want to delete "${title}"?`)) {
            try {
                const response = await fetch(`${pythonURI}/api/suggest`, {
                    ...fetchOptions,
                    method: 'DELETE',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ title }) // Pass title as an object
                });

                if (!response.ok) {
                    throw new Error('Failed to delete book: ' + response.statusText);
                }

                console.log("Book deleted successfully");
                alert('Book deleted successfully!');
                fetchBooks(); // Refresh the book list
            } catch (error) {
                console.error('Error deleting book:', error);
                alert('Error deleting book: ' + error.message);
            }
        } else {
            alert("Deletion canceled");
        }
    }


    async function updateBook(id) {
    const bookContainer = Array.from(document.querySelectorAll('.book'))
        .find(book => book.dataset.id === id);

    if (!bookContainer) {
        alert('Book not found for update.');
        return;
    }

    const currentTitle = bookContainer.querySelector('h3').innerText;
    const currentAuthor = bookContainer.querySelector('p:nth-child(2)').innerText.split(': ')[1];
    const descriptionElement = Array.from(bookContainer.querySelectorAll('p'))
        .find(p => p.innerText.startsWith('Description:'));
    const currentDescription = descriptionElement ? descriptionElement.innerText.replace('Description: ', '') : '';
    const currentGenre = bookContainer.dataset.genre || '';
    const currentCoverUrl = bookContainer.querySelector('img').src;

    // Replace static fields with editable inputs
    bookContainer.innerHTML = `
        <input type="text" id="edit-title" value="${currentTitle}" placeholder="Title">
        <input type="text" id="edit-author" value="${currentAuthor}" placeholder="Author">
        <select id="edit-genre">
            <option value="Classics" ${currentGenre === 'Classics' ? 'selected' : ''}>Classics</option>
            <option value="Fantasy" ${currentGenre === 'Fantasy' ? 'selected' : ''}>Fantasy</option>
            <option value="Nonfiction" ${currentGenre === 'Nonfiction' ? 'selected' : ''}>Nonfiction</option>
            <option value="Historical Fiction" ${currentGenre === 'Historical Fiction' ? 'selected' : ''}>Historical Fiction</option>
            <option value="Suspense/Thriller" ${currentGenre === 'Suspense/Thriller' ? 'selected' : ''}>Suspense/Thriller</option>
            <option value="Romance" ${currentGenre === 'Romance' ? 'selected' : ''}>Romance</option>
            <option value="Dystopian" ${currentGenre === 'Dystopian' ? 'selected' : ''}>Dystopian</option>
            <option value="Mystery" ${currentGenre === 'Mystery' ? 'selected' : ''}>Mystery</option>
        </select>
        <textarea id="edit-description" placeholder="Description">${currentDescription}</textarea>
        <input type="text" id="edit-cover-url" value="${currentCoverUrl}" placeholder="Cover URL">
        <button id="save-update">OK</button>
        <button id="cancel-update">Cancel</button>
    `;

    // Handle Cancel Button
    document.getElementById('cancel-update').addEventListener('click', () => {
        fetchBooks(); // Reload the book list to cancel editing
    });

    document.getElementById('save-update').addEventListener('click', async () => {
        const updatedTitle = document.getElementById('edit-title').value;
        const updatedAuthor = document.getElementById('edit-author').value;
        const updatedGenre = document.getElementById('edit-genre').value;
        const updatedDescription = document.getElementById('edit-description').value;
        const updatedCoverUrl = document.getElementById('edit-cover-url').value;

        const updatedBookData = {
            title: updatedTitle,
            author: updatedAuthor,
            genre: updatedGenre,
            description: updatedDescription,
            cover_url: updatedCoverUrl
        };

        try {
            const response = await fetch(`${pythonURI}/api/suggest/${id}`, {
                ...fetchOptions,
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(updatedBookData)
            });

            if (!response.ok) {
                throw new Error('Failed to update book: ' + response.statusText);
            }

            console.log('Book updated successfully');
            alert('Book updated successfully!');
            fetchBooks(); // Refresh the book list
        } catch (error) {
            console.error('Error updating book:', error);
            alert('Error updating book: ' + error.message);
        }
    });
}
    
    let acceptedBooks = ""; // Variable to store accepted books list

    function updateAcceptedBooksList() {
        const acceptedBooksListElement = document.getElementById("accepted-books-list");
        acceptedBooksListElement.textContent = acceptedBooks.trim() ? acceptedBooks : "None yet.";
    }

    async function acceptBook(title) {
    const bookContainer = Array.from(document.querySelectorAll('.book'))
        .find(book => book.querySelector('h3').innerText === title);

    if (!bookContainer) {
        alert('Book not found for acceptance.');
        return;
    }

    const author = bookContainer.querySelector('p:nth-child(2)').innerText.split(': ')[1];
    const genre = bookContainer.querySelector('p:nth-child(3)').innerText.split(': ')[1];
    const description = bookContainer.querySelector('p:nth-child(4)').innerText.replace('Description: ', '');
    const cover_url = bookContainer.querySelector('img').src;

    const bookData = {
        title: title,
        author: author,
        genre: genre,
        description: description,
        cover_url: cover_url
    };

    try {
        const response = await fetch(`${pythonURI}/api/suggest/accept`, { 
            ...fetchOptions,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(bookData)
        });

        const response2 = await fetch(`${pythonURI}/api/suggest`, {
            ...fetchOptions,
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ title }) 
        });

        if (!response2.ok) {
            throw new Error('Failed to accept book: ' + response2.statusText);
        }

        acceptedBooks += (acceptedBooks ? ", " : "") + title;
        updateAcceptedBooksList(); // Update display
        
        console.log("Book accepted successfully");
        alert('Book accepted successfully!');
        console.log("Book removed from suggestions successfully");
        fetchBooks();  // Refresh book list
    } catch (error) {
        console.error('Error accepting book:', error);
        alert('Error accepting book: ' + error.message);
    }
}   

let rejectedBooks = ""; // Variable to store rejected books list

    function updateRejectedBooksList() {
        const rejectedBooksListElement = document.getElementById("rejected-books-list");
        rejectedBooksListElement.textContent = rejectedBooks.trim() ? rejectedBooks : "None yet.";
    }

async function rejectBook(title) {
    if (confirm(`Are you sure you want to reject "${title}"?`)) {
        try {
            const response = await fetch(`${pythonURI}/api/suggest`, {
                ...fetchOptions,
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ title }) // Pass title as an object
            });

            if (!response.ok) {
                throw new Error('Failed to reject book: ' + response.statusText);
            }

            console.log("Book rejected successfully");
            alert('Book rejected successfully!');
            rejectedBooks += (rejectedBooks ? ", " : "") + title;
            updateRejectedBooksList();
            fetchBooks();
        } catch (error) {
            console.error('Error rejecting book:', error);
            alert('Error rejecting book: ' + error.message);
        }
    } else {
        alert("Rejection canceled");
    }
}

document.getElementById('sort-books').addEventListener('change', fetchBooks);

    // create list at bottom
    async function fetchBooks() {
        try {
            const response = await fetch(new URL(`${pythonURI}/api/suggest/book`), fetchOptions); // Fetch all suggested books
            if (!response.ok) {
                throw new Error('Failed to fetch books: ' + response.statusText);
            }

        const books = await response.json();

        const sortOption = document.getElementById('sort-books').value;
        if (sortOption === "alphabetical") {
            books.sort((a, b) => a.title.localeCompare(b.title));
        }

        const bookList = document.getElementById('book-list-content');
        if (books.length === 0) {
            bookList.innerHTML = '<p style="color: #000000">No books added yet. Fill out the form above to start adding your favorite books!</p>';
            return;
        }

        // Render books
    bookList.innerHTML = books
    .map(
        book => `
        <div class="book" data-id="${book.id}" data-title="${book.title}">
            <h3>${book.title}</h3>
            <p><strong>Author:</strong> ${book.author}</p>
            <p><strong>Genre:</strong> ${book.genre}</p>
            <p><strong>Description:</strong> ${book.description}</p>
            <img src="${book.cover_url}" alt="Cover image of ${book.title}">
            <div class="book-options">
                <button class="updateButton" data-id="${book.id}">Update</button>
                <button class="deleteButton" data-title="${book.title}">Delete</button>
                <button class="acceptButton" data-title="${book.title}">Accept</button>
                <button class="rejectButton" data-title="${book.title}">Reject</button>
            </div>
        </div>
    `
    )
    .join('');
        
        document.querySelectorAll('.updateButton').forEach(button => {
            button.addEventListener('click', (event) => {
                const id = event.target.dataset.id; // Get the title from data attribute
                updateBook(id);
            });
        });
        document.querySelectorAll('.deleteButton').forEach(button => {
            button.addEventListener('click', (event) => {
                const title = event.target.dataset.title; 
                deleteBook(title);
            });
        });
        document.querySelectorAll('.acceptButton').forEach(button => {
            button.addEventListener('click', (event) => {
                const title = event.target.dataset.title; // Get the title from data attribute
                acceptBook(title);
            });
        });
        document.querySelectorAll('.rejectButton').forEach(button => {
            button.addEventListener('click', (event) => {
                const title = event.target.dataset.title; // Get the title from data attribute
                rejectBook(title);
            });
        });

    } catch (error) {
        console.error('Error fetching books:', error);
    }
}


document.addEventListener('DOMContentLoaded', () => {
    fetchBooks();
});

</script>