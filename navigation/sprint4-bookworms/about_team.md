---
layout: page
title: Bookworms About Page
permalink: /bookworms_about/
---

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bookworms Team</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            padding: 20px;
        }
        h1 {
            text-align: center;
            color: #333;
        }
        .container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            justify-content: center;
        }
        .card {
            background-color: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            text-align: center;
            transition: transform 0.2s ease;
        }
        .card:hover {
            transform: scale(1.05);
        }
        .card img {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            margin-bottom: 10px;
        }
        .card h3 {
            font-size: 24px;
            color: #333;
        }
        .card p {
            font-size: 14px;
            color: #555;
            margin-bottom: 10px;
        }
        .card .favorite-books {
            text-align: left;
            margin-top: 10px;
        }
        .card .favorite-books ul {
            padding-left: 20px;
            list-style-type: square;
        }
    </style>
</head>
<body>

    <h1>Meet the Bookworms Team!</h1>

    <div class="container" id="team-container">
        <!-- Cards will be inserted here dynamically -->
    </div>

    <script>
        // Function to fetch and display team information
        async function fetchTeamData() {
            const teamMembers = [
                'avika', 'gabi', 'katherine', 'soumini', 'aditi', 'maryam'
            ];

            const container = document.getElementById('team-container');

            // Fetch data for each team member
            for (let member of teamMembers) {
                const response = await fetch(`http://127.0.0.1:5001/api/${member}`);
                const data = await response.json();

                if (data && data.length > 0) {
                    const memberData = data[0];

                    // Create card element for each member
                    const card = document.createElement('div');
                    card.className = 'card';

                    card.innerHTML = `
                        <img src="https://via.placeholder.com/100" alt="${memberData.FirstName}'s Photo">
                        <h3>${memberData.FirstName} ${memberData.LastName}</h3>
                        <p><strong>Date of Birth:</strong> ${memberData.DOB}</p>
                        <p><strong>Residence:</strong> ${memberData.Residence}</p>
                        <p><strong>Email:</strong> <a href="mailto:${memberData.Email}">${memberData.Email}</a></p>
                        <div class="favorite-books">
                            <strong>Favorite Books:</strong>
                            <ul>
                                ${memberData.Favorite_Books.map(book => `<li>${book}</li>`).join('')}
                            </ul>
                        </div>
                    `;

                    container.appendChild(card);
                }
            }
        }

        // Fetch team data when the page loads
        window.onload = fetchTeamData;
    </script>

</body>
</html>
