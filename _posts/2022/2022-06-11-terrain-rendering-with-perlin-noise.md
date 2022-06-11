---
layout: post
published: true
title: Terrain Rendering with Perlin Noise
date: '2022-06-10 12:00'
image: /post_assets/4/post_image.png
excerpt: Here we will glance into procedural terrain generation.
comments: true
---
In this project we will see how can we render procedural terrains using OpenGL with Perlin Noise. To optimize these calculations we will use geometry shaders to generate terrain on our GPU.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/3/post_image.png">
  <div class="figcaption"><br><br>
  </div>
</div>

## Terrain Rendering

Terrain rendering without many repetitions is a big help while modelling and rendering large and open environments. The main examples for this kind of task is games. While rendering these terrains we need a way to achieve the wanted effect without a performance penalty. 

To outcome these performance challenges, we can use things like culling or chunk rendering, but the most basic performance improvement will come from making these calculations parallel on a GPU. 

Besides from the performance issues, we still need a way to create these good looking non-repeating surfaces. In the next section we will dive into noise to create these effects on our programs.

## Noise

Noise mostly refers to many types of random and troublesome siganls or noises. In our case we need it to create random things happen in our deterministic computers.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/3/noise.png">
  <div class="figcaption"><br><br>
  </div>
</div>

We need some kind of randomness while creating realistic things, because the nature is fully-random. From the scattering of leaves to the movements of the insects. 

<div class="fig figcenter fighighlight">
  <img src="/post_assets/3/random.png">
  <div class="figcaption"><br><br>
  </div>
</div>



## Perlin Noise

## A Problem with Pseudo-Randomness

## Different Frequencies of Same Noise

## Example Camera Movement

## Final Words

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
