---
layout: page
title: Grassy Plains Shader Demo
description: Prototype scene created with various shaders
img: assets/img/project_08/shader_demo_screenshot.png
importance: 3
category: prototypes
---

A small demo scene created in Unity (URP 2022) and Blender overnight.

<iframe width="100%" height="350" src="https://www.youtube.com/embed/TOUCV7deUA0?si=j7zkpcR76VWBPa82" title="YouTube video player" frameborder="0" align="center" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br>

See below for in-depth process breakdown.
<br>
<br>

<h4>Cel-Shader with Additional World Lighting Support</h4>

<i>This shader works in Unity 2021.3+ and URP.</i>

Dependencies:
<ul>
<li>CalcAdditionalDiffuse.shadersubgraph (Returns diffused "Diffuse" light scattered from surfaces hit by additional light sources)</li>
<li>CalcAdditionalSpecular.shadersubgraph (Returns reflective "Specular" light from additional light sources)</li>
<li>GetMainLight.shadersubgraph (Gets lighting values from Main Skybox light source)</li>
<li>GetAdditionalLight.shadersubgraph (Gets lighting values from Additional light sources)</li>
<li>Lighting.hlsl (Provides abstracted functions for GetMainLight and GetAdditionalLight)</li>
</ul>

There are 3 main attributes calculated in this Unlit shader to display realistic lighting upon abstracted textures and colors: 
<ul>
<li>Diffuse light</li>
<li>Specular light</li>
<li>Fresnel effect</li>
</ul>

Along with 9 Parameters to tweak Diffuse, Specular, and Fresnel additives to the output:
<ul>
<li>Ambient Strength = "Glow" multiple that determines how much Ambient Light is emitted from material surface (Manually "Lit" Shader)</li>
<li>Diffuse Color = Color of light that is "Diffused" when light hits material surface</li>
<li>Diffuse Texture = Normal map texture for diffused light</li>
<li>Specular Color = Color of light that is reflected off material surface</li>
<li>Fresnel Color = Color of light that is seen on muted colors of material surface</li>
<li>Fresnel Size = Power factor of Fresnel light</li>
<li>Lighting Cutoff = Determines how harsh the cutoff is between Light and Shadow on material surface</li>
<li>Falloff Amount = How much "bleed" you want between Light and Shadow</li>
<li>Smoothness = Reflectiveness of material surface (used alongside "Ambient Strength" var for Shiny effect)</li>
</ul>

Diffuse Calculations:
<ul>
<li>Get object's current world position</li>
<li>Get MainLight values currently interacting with object</li>
<li>Find "Dot Product" (directional growth of one vector to another) of light data and Normalized world vector</li>
<li>Multiply by Light Attentuation</li>
<li>Multiply by Light Color</li>
<li>Saturate (return value clamped between 0 and 1)</li>
<li>Add Diffuse values from Additional light sources (CalcAdditionalDiffuse)</li>
<li>Perform a series of calculations held within the bounds of "Lighting Cutoff", "Falloff Amount", "Diffuse Color", "Ambient Strength", and "Diffuse Texture" vars to display stark contrast between light and dark shadows (Giving it the "Cel-Shaded" effect).</li>
</ul>

Specular Calculations:
<ul>
<li>Get object's current world position</li>
<li>Get MainLight values currently interacting with object</li>
<li>Add direction to mainLight data and normalize within Object Space</li>
<li>Get Dot Product of Normalized World Vector and Object Space</li>
<li>Saturate (return value clamped between 0 and 1)</li>
<li>Multiply by Power of "Smoothness" value to add light "refraction"/shiny effect</li>
<li>Multiply by Light Attenuation</li>
<li>Multiply by Light Color</li>
<li>Multiply by Saturated Diffuse light so values aren't drastically extreme from one another</li>
<li>Add Specular values from Additional Light Sources (CalcAdditionalSpecular)</li>
<li>Perform a series of calculations held within the bounds of "Lighting Cutoff", "Falloff Amount", and "Specular Color" to intensify specular light for more dramatic light reflections (Giving Light sources "White Dot" reflection on material surface).</li>
</ul>

Fesnel Calculations:
<ul>
<li>Get Reciprocal of "Fresnel Size" var (divide 1 by var)</li>
<li>Add "Fresnel Effect" ("differing reflectance on a surface depending on viewing angle") and multiply its effect by power of Reciprocal</li>
<li>Multiply by Saturated Diffuse light so vlaues aren't drastically extreme from one another</li>
<li>Perform a series of calculations held within the bounds of "Lighting Cutoff", "Falloff Amount", and "Fresnel Color" to display stark contrast between light and dark values on surface border (Giving "Cresting Light" appearance on material surface).</li>
</ul>

Now all calculations are added together, Fog density is added and color is blended to output to apply "Atmospheric Fog" affect based on world 
position from camera so material doesn't stick out in scene.

<strong>NOTE:</strong> My other shader "GrassCelShaderAdditional" copies this shader EXACTLY but simply applies Gaussian noise to UV vertex at base (by Lerping current world position towards guassian noise by Y value so bottom of mesh UVs isn't affected) to create wind affect. Before Cel-Shading this shader was nothing more than texture and color vars and the vertex movement noise which makes it a very simple basis for simulating natural movement upon Shaders.

<br>

<h4>Pixelated Post-Processing Shader</h4>

<i>This shader works in Unity 2021.3+ and URP.</i>

First off, this shader works by being added as a Renderer Feature to the Scriptable Render Pipeline (SRP) through a ScriptableRenderPass class and is thus,
added in Post-Process. This is done through the subsequent "PixelizePass.cs" and "PixelizeFeature.cs" C# scripts.

Then, working in Pixelize.shader (the actual HLSL shader), the shader takes in its required "_MainTex" texture and defines three variables: "_BlockCount",
"_BlockSize", and "_HalfBlockSize".

<ul>
<li>"_BlockCount" = how many pixelated "blocks" will appear per pixel</li>
<li>"_BlockSize" = how large/how many pixels each "block" will cover</li>
<li>"_HalfBlockSize" = Gets half of block size to find center to write on specified pixel</li>
</ul>

Each on-screen pixel's pos and uv is input to the "Varyings" struct and then input to the fragmentation function to divide each pixel UV within each block 
to the "Pixelation" Render Pass (which uses the Renderer Feature to write to the on-screen data).

Pixel UVs are multiplied by the _BlockCount variable and then input to the floor() function to get each block position, which is stored in the "blockPos" var.
The center of each block is calculated by first multiplying (blockPos x _BlockSize) and then adding _HalfBlockSize to locate each block's center.
Then the texture at each block center is sampled using SAMPLE_TEXTURE2D() and passed to the _MainTex with the sampler_point_clamp SamplerState using
each blockCenter position.

Lastly, the textures passed to _MainTex at each block center is returned to be sent to the Renderer Pipeline.
