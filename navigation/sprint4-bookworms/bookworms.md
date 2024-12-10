---
layout: page
title: The Bookworms!
permalink: /bookworms/
---

<video width="1000" height="400" autoplay loop muted>
    <source src="{{site.baseurl}}/navigation/sprint4-bookworms/images/The Bookworms.mp4" type="video/mp4">
</video>

<style>
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

<center>
 <div class="bookshelf">
        <!-- Book 1 -->
        <div class="book">
            <a href="https://www.amazon.com/Inheritance-Games-Jennifer-Lynn-Barnes/dp/0759555400/ref=sr_1_1?crid=7OA29KB25F6I&dib=eyJ2IjoiMSJ9.ijm0MUNlI6X9_y8hGS4bkpk0uh0QpR2E8xPtR4bD3V6STC7xFxCUizAtdrkuUopeewP6TY8hyjcomlBhBoe6cFjgCk5NV2LWt5JXHYSvLFJ6PvSA9hpbriTuwTNnO-1URAmSt89g52RT3rEO5HzT9T63tWYZdYs9jiLXxPQDhDZkp1fQ_kZiEaQY3SMQMUomSO2llowVYTCfgKueFEg3B0WjpfjYu8Fcy0_-aQNBfvg.m5R9xAN_rFXByuoXCJjEe3RmUTR-iNyTnQdL8hruR3E&dib_tag=se&keywords=the+inheritance+games&qid=1733356627&sprefix=the+inheritance+game%2Caps%2C210&sr=8-1" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/inheritance_games.jpg" alt="The Inheritance Games">
            </a>
        </div>
        <!-- Book 2 -->
        <div class="book">
            <a href="https://www.amazon.com/Marrow-Thieves-Cherie-Dimaline/dp/1770864865/ref=sr_1_1?crid=U617D9T2U44I&dib=eyJ2IjoiMSJ9.M44kGTgtiJEaJ5Na8MK8YgL1ccU-rK5zj3HzvoEofiEy7DCGkPqVauJ70bpNoeGR8E4gsur2fYV5C5HL6ImehfqW_nJ3SG99vgYueNEUz1I.0oaMzLQSU--41dVzI45WpOloYM6DWiF_S9VsISQpsDw&dib_tag=se&keywords=the+marrow+thieves&qid=1733356654&sprefix=the+marrow+thieve%2Caps%2C180&sr=8-1" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/the_marrow_thieves.jpg" alt="The Marrow Thieves">
            </a>
        </div>
        <!-- Book 3 -->
        <div class="book">
            <a href="https://www.amazon.com/Silent-Patient-Alex-Michaelides/dp/125030170X/ref=sr_1_1?crid=18IBEDMN50OZ6&dib=eyJ2IjoiMSJ9.RkvX9My_1hiBU_c8TbqGL5oL8sT6kU8hJumACOENHtLPtOJH3SbOyjvdGge8p6tFe56wLjATsPmZgBUeVGTavFc2n5yIV45Hc8ewjpmeKOBAklmphR3UsudmxQzIHgHSmqbSK3cUdZ-xAPYCBnrFSKGKncHFkx1pI-dmtGPGxj1FzCuhSoJWFQI6uAnc-S6JHIrpnUuUzhQXWaqgnWWfSMZojQFHlEIe_wJMaYbCqdM.bd7EUkToYISzp0C1hmHtCy6PgQqU5XkXI8PdxaVDWYQ&dib_tag=se&keywords=the+silent+patient&qid=1733356672&sprefix=the+silent%2Caps%2C189&sr=8-1" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/the_silent_patient.jpg" alt="The Silent Patient">
            </a>
        </div>
        <!-- Add more books as needed -->
        <!-- Book 4 -->
        <div class="book">
            <a href="https://www.amazon.com/Mystwick-School-Musicraft-Jessica-Khoury/dp/132862563X" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/mystwick.jpg" alt="The Mystwick School of Musicraft">
            </a>
        </div>
        <!-- Book 5 -->
        <div class="book">
            <a href="https://www.amazon.com/Harry-Potter-Order-Phoenix-Book/dp/043935806X" target="_blank">
                <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/order_of_phoenix.jpg" alt="Harry Potter: Order of the Pheonix">
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
   }
   /* Sections for each feature */
   .section {
       background-color: #FFC0CB;
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
          <button onclick="location.href='{{site.baseurl}}/navigation/sprint4-bookworms/genre_chatroom'">Enter 💬📚</button>
      </div>
      div class="section">
          <h2>About Us</h2>
          <button onclick="location.href='{{site.baseurl}}/bookworms_about'">Click Here</button>
      </div>
</div>