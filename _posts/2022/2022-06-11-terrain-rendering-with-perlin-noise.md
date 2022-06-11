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
  <img src="/post_assets/4/post_image.png">
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
  <img src="/post_assets/4/noise.png">
  <div class="figcaption"><br><br>
  </div>
</div>

We need some kind of randomness while creating realistic things, because the nature is fully-random. From the scattering of leaves to the distrubition of moss on a wall. 

<div class="fig figcenter fighighlight">
  <img src="/post_assets/4/random.png">
  <div class="figcaption"><br><br>
  </div>
</div>

To achieve this kind of effect on our programs we need pseudo-random algorithms which are not fully random algorithms but trying to achieve same results in our deterministic computers. In this project we will use Perlin Noise which is introcuded by Ken Perlin in 1983. Before introducing the Perlin Noise I want to talk about how can we create our surfaces in GPU using geometry shaders.

## Geometry Shaders

The geometry shaders are used to create primitives like points, lines and triangles. It is executed after vertex shader. It will convert the data coming from vertex shader into some other primitives. It also may emit no primitive as a result of a different algorithm. 

In our case we need a basic surface consists triangles. To do that we can use some feature called instanced rendering. In our OpenGL program on CPU, we don't want to make any calculations. However, we still need to make enough draw calls to populate them into a surface. In the end we want to use geometry shaders to do the work. Thus, we can send points from CPU to GPU for converting them into triangles (triangle strips in our case). However, the surface we wanted to create has lots of polygons to draw, so calling a draw function for each one of them is not efficient. 

The solution is making a single point draw call with an instance count. This call will create multiple copies of our point and will send them to our shaders. To differentiate and place them in their position, we need a way to know which point we are handling. The shader has a built in variable for instanced draw calls just for that. With our basic algorithm in the geometry shader we can easily turn our multiple copies of same point into a surface with triangular faces.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/4/basic_surface.png">
  <div class="figcaption"><br><br>
  </div>
</div>

We created our surface without any fun, so lets have a look into perlin noise to what can we do about it.

## Perlin Noise

Perlin Noise is a procedural pseudo-random texture generation algorithm to improve the realism into computer graphic programs. The algorithm takes inputs in 2D or 3D to generate a random floating point number in the range of (-1, 1). 

The algorihm generates the output in two steps. The first step is choosing gradients from a precomputed selections. We select these gradient vectors for each vertex of our imaginary grid.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/4/grid.png">
  <div class="figcaption"><br><br>
  </div>
</div>

In the next step we put our input into the grid and calculate the weights of these gradients and accumulate the result of the dot product of these vectors with the difference vectors with the weights that we have calculated.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/4/grad_weight.png">
  <div class="figcaption"><br><br>
  </div>
</div>

In the end we will have a texture like this if we want to map the color of the pixels in greyscale to the result of perlin noise algorithm.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/4/perlin.png">
  <div class="figcaption"><br> A texture output of perlin noise algorithm.<br>
  </div>
</div>

In the next section we will apply these randomness in our surface as height values.

## A Problem with Pseudo-Randomness

## Different Frequencies of Same Noise

## Example Camera Movement

## Final Words


