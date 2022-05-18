---
layout: post
published: false
title: Creating Reflections with OpenGL
---
In this project we will see how can we create reflective surfaces on modern OpenGL without the help of raytracing. To do so we will use a technique called cube mapping to map the environment to a texture. We will also see some other cool-effects we can achieve with cubemaps.

## Introduction to Cubemaps

<div class="fig figcenter fighighlight">
  <img src="/post_assets/3/cube.gif">
  <div class="figcaption"><br><br>
  </div>
</div>

A cubemap is basically a texture pack with six square layers, each layer is the projection of our environment with 6 different angles. These angles are representing a cube when it is loaded in a 3D environment. 

We can achieve same kind of environmental mapping with spheres, but it has problems such as distortion and computational inefficiency. 

The cubemaps can be created beforehand to represent some backgrounds in computer graphics applications. However, they also can be created in real-time to map the virtual environment to a texture to create effects like reflections. 

## Creating a Skybox

We can use real life equirectangular projection (panorama) images to create cubemaps. To do so we create a special mapping which calculates the spherical coordinates of the pixels on the cubemap. Then we take these spherical coordinates then lookup the related pixels using a interpolation method in the equirectangular projection images.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/3/hdr.png">
  <div class="figcaption"><br><br>
  </div>
</div>

<div class="fig figcenter fighighlight">
  <img src="/post_assets/3/tocubemap.png">
  <div class="figcaption"><br><br>
  </div>
</div>


## Dynamic Cubemaps for Reflections

## Final Thoughts


