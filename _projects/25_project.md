---
layout: page
title: Photonics
description: 
img: assets/img/convergence_assets/Image Sequence_013_0000.png
importance: 1
category: games
images:
  slider: true 
---

<link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">
<style>
	.inline {
	  display: flex;
	  align-items: center; /* Aligns items vertically */
	  gap: 120px; /* Optional: Adds space between elements */
	}
	.dropdown {
	  font-size: 18px
	}
</style>

<div class="inline">
  <h5>2024 | Unity | <strong class="highlight-text">Art / UI / Animation</strong></h5>
  <button class="theme-button" onclick="window.location.href='https://fergo310.itch.io/photonics';">View Itch Page</button>
</div>
<hr style="height:5pt; visibility:hidden;"/>

<iframe width="100%" height="500" src="https://www.youtube.com/embed/QUCEfdIKsN0?si=co5m11D5SAczWX_H" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>
Convergence began as a small particle simulation that evolved into a planetary game focused on velocity and mass. As Lead Artist, I designed the UI, sprites, and VFX to reinforce the feeling of immensity—growing from a tiny particle to a massive planetary body—using camera scaling, sprite transitions, and a 12-stage sprite sheet that formed a dynamic, ever-changing visual experience.
<br>

<!--
<details>
<summary class="dropdown"><strong>Read Full Development Process</strong></summary>
<br>
<h2>Pre-Production</h2>
Convergence started as a small gravity particle simulation created by my friend <a href="https://piggytek.com/main/">Eric Patrick</a> in his spare time. He saw its potential, and decided to pitch it to our school's Video Game Development Club where he recruited the help of narrative designer <a href="https://www.linkedin.com/in/adhi-kona-46b663258/">Adhi Kona</a> and experienced programmer <a href="https://davidlh123.itch.io/">David Huynh</a>.

<br>
<br>
<iframe width="100%" height="500" src="https://www.youtube.com/embed/sZrK8oXEFe8?si=WZLmy062CnhaJ-Cq" title="YouTube video player" frameborder="0" align="center" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>
<br>
<h3 style="margin-left: 25px;">Gathering References</h3>
<p style="margin-left: 25px;">Excited to explore the visuals of space, planetary bodies, and monumentalism through this project, I turned to Netflix documentaries such as Our Universe, Alien Worlds, and A Trip to Infinity for ideas on how to present the vastness of space within a 2D game. Not wanting to overwhelm a relatively simple game with highly detailed assets, I made the decision at this stage to design the visuals in Vector art and began experimenting with styles akin to mobile games.</p>

<br>
<h3 style="margin-left: 25px;">Concept Art + UI Mockups</h3>
<p style="margin-left: 25px;">After laying out techical requirements for atmospheric fog and sprite transitions for planets based on size, I began creating visual and UI concepts in Illustrator in an iterative design with my team, achieving a higher fidelity with each passover.</p>

<br>
<swiper-container keyboard="true" scrollbar="true" rewind="true">
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Mockup_240428_02.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Mockup_240429_07.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Pause_Mockup_240429_08.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
</swiper-container>
<br>

<h3 style="margin-left: 25px;">Establishing Style</h3>
<p style="margin-left: 25px;">Now having a general idea of what the game was going to look like, I then began to explore sprite variations to portray a style that was both simplistic, yet captured the beauty of monumental detail at a distance.</p>

<br>
<swiper-container keyboard="true" scrollbar="true" rewind="true">
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Icons_Draft_02_240429.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Icons_240513.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Icons_240513_03_Refs.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
</swiper-container>

<br>
<h2>Production</h2>

<br>
<h3 style="margin-left: 25px;">Implementing Sprites, VFX, and Sound Effects</h3>
<p style="margin-left: 25px;">Once all assets were concepted, iterated, and finalized we began to implement the various atmospheric fog, danger indicator, and UI systems we had laid out in our design document. At this point of implementation I took the liberty to develop the sound effects for planet collisions, abilities, and UI interaction. Working in Audacity and pulling references from various sites, all of the sound effects you hear in the game were actually developed over the final week of development.</p>

<br>
<h3 style="margin-left: 25px;">Playtesting</h3>
<p style="margin-left: 25px;">Taking the opportunity to playtest our game at bi-weekly playtest sessions held by our school's Video Game Development Club, our team regularly tested for bugs, UI flow, and visual indicators to refine our systems throughout development.</p>

<br>
<h3 style="margin-left: 25px;">Polish</h3>
<p style="margin-left: 25px;">Over the last week of development, we began doing passovers on all assets, sfx, UIs, and a 24 hour marathon of debugging and tweaks. These screenshots are some samples of the final product.</p>
<br>
<swiper-container keyboard="true" scrollbar="true" rewind="true">
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/convergencetest_01.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Image Sequence_038_0000.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Image Sequence_041_0000.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Image Sequence_036_0000.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/Image Sequence_034_0000.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/convergence_assets/image-0004.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
</swiper-container>

<br>
<h2>Shipping</h2>

<br>
<p style="margin-left: 25px;"><button class="theme-button" onclick="window.location.href='https://thepigguy.itch.io/convergence';">View Itch Page</button></p>

<br>
<h3 style="margin-left: 25px;">Convention Booth</h3>
<br>
<img src="../../assets/img/convergence_assets/20240603_130551.jpg" width="100%">
<div class="caption">Me getting those display colors juuuuusssst right.</div>
<br>
</details>
-->