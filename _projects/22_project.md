---
layout: page
title: Project Ether
description: 3rd-person stealth game using AnimGraph, Animation Montages, and Blendspaces
img: assets/img/ether_assets/pe_titlecard.png
importance: 2
category: games
images:
  slider: true 
---

<link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">

30-minute vertical slice featuring approachable stealth mechanics, diverse story, and unique visuals. 

<swiper-container keyboard="true" scrollbar="true" rewind="true">
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/ether_assets/PE_8.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/ether_assets/PE_6.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/ether_assets/PE_10.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
</swiper-container>

<br>
<h2>Contributions</h2>
Towards the end of this Capstone group's development window, they were still experiencing a lot of animation bugs and their main 3D animator had just taken leave. Luckily, through a mutual friend who was heavily involved in the game's systems programming, I was brought along and was able to resolve many of these issues over the course of 2 weeks.
A quick summary of my work involved:
<ul>
<li>Debug critical animation, movement, and texture bugs with models</li>
<li>Debugged Animation Montage triggers</li>
<li>Smoothed out AnimGraph states</li>
<li>Fixed faulty render textures produced from Blender to Unreal pipeline</li>
</ul>
While I worked, I also kept a list of changes I pushed to Perforce to report to my team lead (see below).
<ul>
<strong>04/06/2024 6:19 PM</strong>
<li>Added EtherOn and EtherOff anims</li>
<li>Fixed logic in AnimGraph -> Locomotion</li>
<li>Created "Disable Ether" function in BP_ThirdPersonCharacter - > "Ether Functionality" to trigger when Energy depletes to 0 (so game doesn't hang anymore)"</li>
<br>
<strong>04/10/2024 2:00 AM</strong>
<li>zoomed out cam and aimed it down to show full model</li>
<li>implemented couch_walk_idle anim</li>
<li>set AM_Zhay_HexShoot slot to UpperbodySlot so it only blends the upperbody</li>
<br>
<strong>04/10/2024 11:43 PM</strong>
<li>Added 0.6 Delay before "Set Dying" in Guard EventGraph and removed link between "Takedown" and "Dead" states in "GuardBotAnimBP", which fixed Takedown timing and overshooting "Takedown" state</li>
<li>Reimported Zhay_Takedown with -90 rotation as "Zhay_Takedown_Fixed" to rotated animation</li>
<li>Set "Ether" bool as deciding condition in "On Component Begin Overlap (Sight") event, since "Hex Toggle" var wasn't working</li>
<li>Moved "Enable Input" to play after 2nd Delay in Event Interface Interact and set Delay to "1.5" seconds for timing</li>
<li>Fixed Guards idle walking by adding an "Idle" state to GuardBotAnimBP, which triggers when not moving</li>
<li>Created "isStunned" bool in Guard EventGraph, which is set to true when "Stun Guard" is called and set to false after Event Interface Interact is called. This is checked when "On Component Begin Overlap (AttackHurtbox)" is triggered and is a condition of attacking, elimnating the event of a player stunning a Guard and still getting attacked.</li>
<br>
<strong>04/20/2024 7:24 PM</strong>
<li>Fixed dead Guard models being rotated when Ether Off by creating If/Branch conditional in "Look In Direction" section of Guard EventGraph</li>
<li>Fixed Guard moonwalk by skipping "Set Actor Rotation" in "Scan" MotionWarp</li>
</ul>
<br>

<h2>Demo Video</h2>
<div align="center"><iframe width="100%" height="350" src="https://www.youtube.com/embed/GlWbBLLwht4?si=682XQCs_byLhUmOr" title="YouTube video player" frameborder="0" align="center" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>
<div align="center"><button class="theme-button" onclick="window.location.href='https://pjheric.itch.io/project-ether';">View Itch Page</button></div>
<br>