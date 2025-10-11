---
layout: page
title: "Paintings"
permalink: /paintings/
---

# Paintings Gallery

Welcome to my collection of paintings. Click on an image to view it in full size.

<style>
.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
  margin-top: 2rem;
}

.gallery img {
  width: 100%;
  height: auto;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.2);
  transition: transform 0.2s ease;
}

.gallery img:hover {
  transform: scale(1.03);
}
</style>

<div class="gallery">
  <a href="{{ '/paintings/painting_1.jpg' | relative_url }}" target="_blank">
    <img src="{{ '/paintings/painting_1.jpg' | relative_url }}" alt="Painting 1">
  </a>
  <a href="{{ '/paintings/painting_2.jpg' | relative_url }}" target="_blank">
    <img src="{{ '/paintings/painting_2.jpg' | relative_url }}" alt="Painting 2">
  </a>
  <a href="{{ '/paintings/painting_3.jpg' | relative_url }}" target="_blank">
    <img src="{{ '/paintings/painting_3.jpg' | relative_url }}" alt="Painting 2">
  </a>
  <a href="{{ '/paintings/painting_4.jpg' | relative_url }}" target="_blank">
    <img src="{{ '/paintings/painting_4.jpg' | relative_url }}" alt="Painting 2">
  </a>


  <!-- Add more images as needed -->
</div>

