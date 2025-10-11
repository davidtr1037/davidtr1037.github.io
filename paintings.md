---
layout: page
title: "Paintings"
permalink: /paintings/
---

<style>
.gallery {
  column-count: 2; /* adjust number of columns */
  column-gap: 1rem;
}

.gallery img {
  width: 100%;
  height: auto;
  border-radius: 8px;
  margin-bottom: 1rem;
  box-shadow: 0 2px 6px rgba(0,0,0,0.2);
  cursor: pointer;
  transition: transform 0.2s ease;
}

.gallery img:hover {
  transform: scale(1.02);
}

/* Lightbox styles */
#lightbox {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: none;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

#lightbox img {
  max-width: 90%;
  max-height: 90%;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(255,255,255,0.2);
}

#lightbox .close-btn {
  position: absolute;
  top: 20px;
  right: 30px;
  font-size: 32px;
  font-weight: bold;
  color: white;
  cursor: pointer;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  line-height: 1;
  transition: background 0.2s;
}

#lightbox .close-btn:hover {
  background: rgba(255, 255, 255, 0.2);
}

@media (max-width: 768px) {
  .gallery {
    column-count: 1;
  }
}
</style>

### *Acrylic Paintings*

<div class="gallery">
  <img src="{{ '/paintings/painting_1.jpg' | relative_url }}" alt="Painting 1" onclick="openLightbox(this)">
  <img src="{{ '/paintings/painting_2.jpg' | relative_url }}" alt="Painting 2" onclick="openLightbox(this)">
  <img src="{{ '/paintings/painting_3.jpg' | relative_url }}" alt="Painting 3" onclick="openLightbox(this)">
  <img src="{{ '/paintings/painting_4.jpg' | relative_url }}" alt="Painting 3" onclick="openLightbox(this)">
</div>

<div id="lightbox">
  <span class="close-btn" onclick="closeLightbox()">&times;</span>
  <img src="" id="lightbox-img">
</div>

<script>
function openLightbox(img) {
  const lightbox = document.getElementById('lightbox');
  const lightboxImg = document.getElementById('lightbox-img');
  lightboxImg.src = img.src;
  lightbox.style.display = 'flex';
}

function closeLightbox() {
  document.getElementById('lightbox').style.display = 'none';
}
</script>
