---
layout: page
title: The Bookworms!
permalink: /bookworms/
---
<style>
.carousel {
    position: relative;
    overflow: hidden;
    max-width: 800px;
    margin: auto;
}
.carousel-track {
    display: flex;
    transition: transform 0.5s ease-in-out;
}
.carousel-image {
    width = 100px;
    height = 300px;
    flex-shrink: 0;
}
.carousel-button {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: rgba(0, 0, 0, 0.5);
    color: white;
    border: none;
    padding: 10px;
    cursor: pointer;
}
.carousel-button.prev {
    left: 10px;
}
.carousel-button.next {
    right: 10px;
}
</style>

<div class="carousel">
    <div class="carousel-track">
        <img src="{{site.baseurl}}/navigation/sprint4-bookworms/images/inheritance_games.jpg" alt="Inheritance Games" class="carousel-image">
        <img src="image2.jpg" alt="Image 2" class="carousel-image">
        <img src="image3.jpg" alt="Image 3" class="carousel-image">
        <!-- Add more images as needed -->
    </div>
    <button class="carousel-button prev">‹</button>
    <button class="carousel-button next">›</button>
</div>

<script>
const track = document.querySelector('.carousel-track');
const images = Array.from(track.children);
const prevButton = document.querySelector('.carousel-button.prev');
const nextButton = document.querySelector('.carousel-button.next');

let currentIndex = 0;

function updateCarousel() {
    const imageWidth = images[0].getBoundingClientRect().width;
    track.style.transform = `translateX(-${currentIndex * imageWidth}px)`;
}

prevButton.addEventListener('click', () => {
    currentIndex = (currentIndex > 0) ? currentIndex - 1 : images.length - 1;
    updateCarousel();
});

nextButton.addEventListener('click', () => {
    currentIndex = (currentIndex < images.length - 1) ? currentIndex + 1 : 0;
    updateCarousel();
});
</script>


<a href="{{site.baseurl}}/navigation/sprint4-bookworms/genre_chatroom"
  style="background-color: rgb(135, 206, 250); color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
  Genre Chatroom 
</a>