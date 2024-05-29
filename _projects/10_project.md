---
layout: page
title: Scene using various shaders
description: Grass Vertex Shader (ShaderGraph), Cel Shader (ShaderGraph), and Pixelated Post-Processing Shader (HLSL)
img: assets/img/project_08/shader_demo_screenshot.png
importance: 3
category: prototypes
---

A small project created in Unity (URP 2022) for my Advanced Unity Programming course (GDIM 33). All models were created in Blender.

<iframe width="560" height="315" src="https://www.youtube.com/embed/TOUCV7deUA0?si=j7zkpcR76VWBPa82" title="YouTube video player" frameborder="0" align="center" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br>

As there are too many files to realistically include, please see below for the shaders README files instead. Code availabe upon request.

CelShaderAdditional_README.txt
```
Cel-Shader with Additional World Lighting Support (CelShaderAdditional.shadergraph)

This shader works in Unity 2021.3+ and URP.

Dependencies:
-CalcAdditionalDiffuse.shadersubgraph (Returns diffused "Diffuse" light scattered from surfaces hit by additional light sources)
-CalcAdditionalSpecular.shadersubgraph (Returns reflective "Specular" light from additional light sources)
-GetMainLight.shadersubgraph (Gets lighting values from Main Skybox light source)
-GetAdditionalLight.shadersubgraph (Gets lighting values from Additional light sources)
-Lighting.hlsl (Provides abstracted functions for GetMainLight and GetAdditionalLight)

There are 3 main attributes calculated in this Unlit shader to display realistic lighting upon abstracted textures and colors: 

-Diffuse light
-Specular light
-Fresnel effect

Along with 9 Parameters to tweak Diffuse, Specular, and Fresnel additives to the output:

-Ambient Strength = "Glow" multiple that determines how much Ambient Light is emitted from material surface (Manually "Lit" Shader)
-Diffuse Color = Color of light that is "Diffused" when light hits material surface
-Diffuse Texture = Normal map texture for diffused light
-Specular Color = Color of light that is reflected off material surface
-Fresnel Color = Color of light that is seen on muted colors of material surface
-Fresnel Size = Power factor of Fresnel light
-Lighting Cutoff = Determines how harsh the cutoff is between Light and Shadow on material surface
-Falloff Amount = How much "bleed" you want between Light and Shadow
-Smoothness = Reflectiveness of material surface (used alongside "Ambient Strength" var for Shiny effect)

Diffuse Calculations:

	1. Get object's current world position
	2. Get MainLight values currently interacting with object
	3. Find "Dot Product" (directional growth of one vector to another) of light data and Normalized world vector
		4. Multiply by Light Attentuation
			5.Multiply by Light Color
	6. Saturate (return value clamped between 0 and 1)
	7. Add Diffuse values from Additional light sources (CalcAdditionalDiffuse)
	8. Perform a series of calculations held within the bounds of "Lighting Cutoff", "Falloff Amount", "Diffuse Color", "Ambient Strength", and
	   "Diffuse Texture" vars to display stark contrast between light and dark shadows (Giving it the "Cel-Shaded" effect).

Specular Calculations:

	1. Get object's current world position
	2. Get MainLight values currently interacting with object
	3. Add direction to mainLight data and normalize within Object Space
	4. Get Dot Product of Normalized World Vector and Object Space
	5. Saturate (return value clamped between 0 and 1)
	6. Multiply by Power of "Smoothness" value to add light "refraction"/shiny effect
		7. Multiply by Light Attenuation
			8. Multiply by Light Color
	9. Multiply by Saturated Diffuse light so values aren't drastically extreme from one another
	10. Add Specular values from Additional Light Sources (CalcAdditionalSpecular)
	11. Perform a series of calculations held within the bounds of "Lighting Cutoff", "Falloff Amount", and "Specular Color" to intensify specular
		light for more dramatic light reflections (Giving Light sources "White Dot" reflection on material surface).

Fesnel Calculations:

	1. Get Reciprocal of "Fresnel Size" var (divide 1 by var)
	2. Add "Fresnel Effect" ("differing reflectance on a surface depending on viewing angle") and multiply its effect by power of Reciprocal
	3. Multiply by Saturated Diffuse light so vlaues aren't drastically extreme from one another
	4. Perform a series of calculations held within the bounds of "Lighting Cutoff", "Falloff Amount", and "Fresnel Color" to display stark contrast
	   between light and dark values on surface border (Giving "Cresting Light" appearance on material surface).

Now all calculations are added together, Fog density is added and color is blended to output to apply "Atmospheric Fog" affect based on world 
position from camera so material doesn't stick out in scene.

*NOTE*: My other shader "GrassCelShaderAdditional" copies this shader EXACTLY but simply applies Gaussian noise to UV vertex at base 
        (by Lerping current world position towards guassian noise by Y value so bottom of mesh UVs isn't affected) to create wind affect. 
		Before Cel-Shading this shader was nothing more than texture and color vars and the vertex movement noise which makes it a very
		simple basis for simulating natural movement upon Shaders.
```

PixelizedShader_README.txt
```
Pixelated Post-Processing Shader (Pixelize.shader)

This shader works in Unity 2021.3+ and URP.

First off, this shader works by being added as a Renderer Feature to the Scriptable Render Pipeline (SRP) through a ScriptableRenderPass class and is thus,
added in Post-Process. This is done through the subsequent "PixelizePass.cs" and "PixelizeFeature.cs" C# scripts.

Then, working in Pixelize.shader (the actual HLSL shader), the shader takes in its required "_MainTex" texture and defines three variables: "_BlockCount",
"_BlockSize", and "_HalfBlockSize".

- "_BlockCount" = how many pixelated "blocks" will appear per pixel
- "_BlockSize" = how large/how many pixels each "block" will cover
- "_HalfBlockSize" = Gets half of block size to find center to write on specified pixel

Each on-screen pixel's pos and uv is input to the "Varyings" struct and then input to the fragmentation function to divide each pixel UV within each block 
to the "Pixelation" Render Pass (which uses the Renderer Feature to write to the on-screen data).

Pixel UVs are multiplied by the _BlockCount variable and then input to the floor() function to get each block position, which is stored in the "blockPos" var.
The center of each block is calculated by first multiplying (blockPos x _BlockSize) and then adding _HalfBlockSize to locate each block's center.
Then the texture at each block center is sampled using SAMPLE_TEXTURE2D() and passed to the _MainTex with the sampler_point_clamp SamplerState using
each blockCenter position.

Lastly, the textures passed to _MainTex at each block center is returned to be sent to the Renderer Pipeline.
```