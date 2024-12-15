---
layout: page
title: Physics Shader Demo
description: Cloth and Soft-Body Physics Shaders
img: assets/img/project_09/physics_demo_screenshot.png
importance: 4
category: prototypes
---

A small demo scene created in Unity (URP 2022) and Blender over a weekend.

<iframe width="100%" height="350" src="https://www.youtube.com/embed/HS1fY9ULopY?si=m3zbXy9h0GaTqVbr" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br>

See below for in-depth process breakdown.
<br>
<br>

<h4>Yellow Raincoat - Cloth Component:</h4>

I began the process by creating models of my player character and yellow coat in Blender 3.4. After rigging the player character model, I exported both to Unity, set the animations in an Animator component, and then added the Yellow coat model over the player character model. Then I added the Cloth component to the coat model and constrained it around the player characters neck, working my way downward at each horizontal vertex loop with increasing constraint values (higher value = looser cloth). This worked initially, however because I was using a CharacterController component to move my player character and the Cloth is a part of Unity's Physics engine, I experienced "jitter" in my initial build. Essentially, my player character was moving manually in Update() while my coat was moving with physics in FixedUpdate(), so to mediate this I applied my player character's movement in FixedUpdate() and then set the FollowCam (Cinemachine component) to track the player character in FixedUpdate() as well, removing jitter for both my models and my camera.

<br>
<h4>Jelly Objects - Jellyfier.cs, JellyVertex.cs, Box/Sphere/Cylinder Collider component, Rigidbody component:</h4>

Since the Cloth component for Unity was mainly centered around simulating loose cloth, I had to turn to other sources to simulate softbody physics. What I came up with was a retainer script to hold several methods including GetCurrentDisplacement() which gets a Vector3 pos of vertices, UpdateVelocity() which applies "bounce" distortion, Settle() which dissipates bounce and returns to the original pos, and ApplyPressureToVertex() to apply velocity when colliding with another object. This then all came together in the Jellyfier.cs script, which gets its Parent object's mesh and creates a list of each vertice, applies forces to each individual vertice, and uses ApplyPressureToPoint() to apply forces to specific vertices that experienced a collision. Then, for some visual flair, I added a Jelly shader to give the objects a jelly-like apperance and then a Wireframe shader to display object mesh vertice and edges (to make it easier to see softbody physics in action). Then I simply added Collider components (Box, Sphere, Cylinder, etc.) to account for collisions and Rigidbody components to enact Physics upon the objects.
