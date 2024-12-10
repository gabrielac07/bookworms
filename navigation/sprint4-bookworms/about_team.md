---
layout: page
title: Bookworms About Page
permalink: /bookworms_about/
---


<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Team About Page</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #0B3954;
            color: #333;
        }
        .navbar {
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 20px;
            background-color: #EDE4D9;
        }
        .navbar h1 {
            margin: 0;
            font-size: 24px;
            color: #4B4A40;
        }
        .navbar button {
            padding: 10px;
            font-size: 16px;
            background-color: #C3A382;
            border: none;
            color: white;
            cursor: pointer;
            border-radius: 5px;
        }
        .navbar button:hover {
            background-color: #444;
        }
        #team-about {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            margin: 20px auto;
            padding: 20px;
            max-width: 1200px;
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .team-member {
            flex: 1 1 calc(33.333% - 20px);
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 8px;
            background-color: #fff;
            box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
        }
        .team-member h2 {
            margin: 0 0 10px;
            font-size: 20px;
            color: #4B4A40;
        }
        .team-member p {
            margin: 5px 0;
            color: #555;
        }
    </style>
</head>
<body>
    <div class="navbar">
        <h1>Team About Page</h1>
        <button onclick="refreshPage()">Refresh</button>
    </div>
    <div id="team-about">
        <!-- Team member data will be dynamically inserted here -->
    </div>
    <script>
        // Function to fetch data from the Flask API and display it
        function fetchTeamMember(name) {
            const apiUrl = `http://127.0.0.1:5000/api/${name}`;
            // Fetch data from the Flask API
            fetch(apiUrl)
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    return response.json();
                })
                .then(data => {
                    // Assuming the API returns an array with one object
                    const member = data[0];
                    // Create HTML content
                    const memberHTML = `
                        <div class="team-member">
                            <h2>${member.FirstName} ${member.LastName}</h2>
                            <p><strong>Date of Birth:</strong> ${member.DOB}</p>
                            <p><strong>Residence:</strong> ${member.Residence}</p>
                            <p><strong>Email:</strong> ${member.Email}</p>
                            <p><strong>Favorite Books:</strong> ${member.Favorite_Books.join(', ')}</p>
                        </div>
                    `;
                    // Append to the "team-about" container
                    document.getElementById('team-about').innerHTML += memberHTML;
                })
                .catch(error => {
                    console.error('There was a problem with the fetch operation:', error);
                });
        }
        // Fetch data for each team member
        const teamMembers = ['avika', 'gabi', 'katherine', 'soumini', 'aditi', 'maryam'];
        teamMembers.forEach(fetchTeamMember);
        // Function to refresh the page
        function refreshPage() {
            document.getElementById('team-about').innerHTML = '';
            teamMembers.forEach(fetchTeamMember);
        }
    </script>
</body>
</html>
