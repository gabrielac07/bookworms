---
layout: page
title: Once Upon A Shelf
permalink: /bookworms/
---

<video width="1000" height="400" autoplay loop muted>
    <source src="{{site.baseurl}}/navigation/sprint4-bookworms/images/The Bookworms.mp4" type="video/mp4">
</video>

<center>
<h3> View some top picks! 👇🏼 </h3>
</center>
<br>

<style>
    .bookshelf {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            max-width: 800px;
            padding: 20px;
            background: #e6be9a;
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

<center>
 <div class="bookshelf" >
        <!-- Book 1 -->
        <div class="book">
            <a href="https://www.amazon.com/Little-Bantam-Classics-Louisa-Alcott/dp/0553212753" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/littlewomen.jpg" alt="Little Women">
            </a>
        </div>
        <!-- Book 2 -->
        <div class="book">
            <a href="https://www.amazon.com/Salt-Sea-Ruta-Sepetys/dp/0142423629" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/salttothesea.jpg" alt="Salt to the Sea">
            </a>
        </div>
        <!-- Book 3 -->
        <div class="book">
            <a href="https://www.amazon.com/Silent-Patient-Alex-Michaelides/dp/125030170X/ref=sr_1_1" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/the_silent_patient.jpg" alt="The Silent Patient">
            </a>
        </div>
        <!-- Add more books as needed -->
        <!-- Book 4 -->
        <div class="book">
            <a href="https://www.amazon.com/Educated-Memoir-Tara-Westover/dp/0399590528" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/educated.jpg" alt="Educated">
            </a>
        </div>
        <!-- Book 5 -->
        <div class="book">
            <a href="https://www.amazon.com/Harry-Potter-Sorcerers-Stone-Book/dp/1338878921" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/sorcerers_stone.jpg" alt="Harry Potter: The Sorcerer's Stone">
            </a>
        </div>
         <!-- Book 6 -->
        <div class="book">
            <a href="https://www.amazon.com/Good-Girls-Guide-Murder/dp/1984896369" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/girl_murder.jpg" alt="A good girl's guide to murder">
            </a>
        </div>
    </div>
<br>

<style>
   .container {
       display: flex;
       flex-wrap: wrap;
       justify-content: space-around;
       gap: 10px;
       padding: 5px;
       max-width: 1200px;
       margin: 0 auto;
       color: black;
   }
   /* Sections for each feature */
   .section {
       background-color: #e6be9a;
       border-radius: 10px;
       padding: 20px;
       width: 800px;
       text-align: center;
       box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
   }
</style>


  <div class="container">
      <div class="section">
          <h2>Profile</h2>
          <p>Add to your profile!</p>
          <button onclick="location.href='{{site.baseurl}}/login'">Login</button>
          <button onclick="location.href='{{site.baseurl}}/bookworms_profile'">Edit</button>
      </div>
      <div class="section">
          <h2>Genre Chatroom</h2>
          <p>Explore genres!</p>
          <button onclick="location.href='{{site.baseurl}}/navigation/sprint4-bookworms/genre_chatroom'" >Enter 💬📚</button>
      </div>
      <div class="section">
          <h2>About Us</h2>
          <button onclick="location.href='{{site.baseurl}}/bookworms_about'">Click Here</button>
      </div>
</div>