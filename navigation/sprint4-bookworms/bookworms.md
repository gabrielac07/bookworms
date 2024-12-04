---
layout: page
title: The Bookworms!
permalink: /bookworms/
---
<video width="1000" height="400" autoplay loop muted>
<source src="{{site.baseurl}}/navigation/sprint4-bookworms/images/The Bookworms.mp4" type="video/mp4">
</video>
<style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f4f4f9;
        }

        .bookshelf {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            max-width: 800px;
            padding: 20px;
            background: #d1ccc0;
            border: 2px solid #aaa;
            border-radius: 10px;
            box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.2);
        }

        .book {
            position: relative;
            width: 100px;
            height: 150px;
            perspective: 1000px; /* For 3D effect */
            text-align: center;
            cursor: pointer;
        }

        .book img {
            width: 100%;
            height: 100%;
            border-radius: 5px;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .book:hover img {
            transform: scale(1.2) rotate(5deg);
            box-shadow: 5px 10px 15px rgba(0, 0, 0, 0.3);
        }

        .book a {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            text-decoration: none;
            color: transparent;
        }
    </style>
<br>
<a href="{{site.baseurl}}/navigation/sprint4-bookworms/genre_chatroom"
  style="background-color: rgb(135, 206, 250); color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
  Genre Chatroom 
</a>

<a href="{{site.baseurl}}/navigation/sprint4-bookworms/bookworms_profile"
  style="background-color: rgb(135, 206, 250); color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
  Edit Profile 
</a>