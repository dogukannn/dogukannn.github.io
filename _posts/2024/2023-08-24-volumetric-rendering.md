---
layout: post
published: true
title: Adventures With CPU Ray Tracer - The Beginning
date: '2024-10-20 12:00'
image: /post_assets/7/post_image.png
excerpt: Here we will create a scene with a CPU ray tracer.
comments: true
share-img: /post_assets/7/post_image.png
---

When I started my journey with Computer Graphics, I came across a fun little book called "Ray Tracing in One Weekend". I was mesmerized by the output images that contain some good looking spheres. I followed that book and implemented a basic ray tracer with different materials and sampling. The book only covered scenes with spheres for simplicity. That journey led me to some interesting topics such as importance sampling, insides of real world cameras and properties of light. With the start of my Masters Degree, I jumped back on this journey with a broader vision and more importantly "triangles".

<div class="fig figcenter fighighlight">
  <img src="/post_assets/7/ray_traced_spheres.png">
  <div class="figcaption"><br>My result from "Ray Tracing in One Weekend"<br>
  </div>
</div>


# Starting from the Basics

In this version, we started with a simpler abstraction over ray tracing, every pixel is sampled once and every light source is checked for basic Blinn-Phong shading calculations. The basic material has diffuse, specular and ambient reflectance values for each color channel. I implemented the Möller–Trumbore intersection for triangle ray intersections, and used standart library features like future and async for multithreaded implementation.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/7/bunny.png">
  <div class="figcaption"><br>Bunny with one light source and basic material, rendered in 1062 ms<br>
  </div>
</div>

# Mirror Materials 

For mirror materials, after the intersection, a reflected ray is traced for calculating the light coming from scene. In practice this could happen infinite times, so we need a limit for the traced ray reflected from the mirror materials.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/7/spheres_mirror.png">
  <div class="figcaption"><br>Sphere with mirror material, rendered in 589 ms<br>
  </div>
</div>

# Dielectric and Conductor Materials

For transparent objects, we need to trace a refracted ray from the intersection point in addition to the reflected ray. We can calculate the direction of the refracted ray according to Snell's Law. 
After this calculation we need to trace two more rays, however to decide the energy distribution (i.e. which ray carries more light towards the camera) we can use Fresnel equations. This equations specify an object's reflections for lights polarization states. For refracted rays we also need to calculate attenuation to calculate the absorbtion of the energy inside of a transparent material, we will use Beer's Law for this calculation. With all things added, we can output nice looking transparent objects.

For conductors, we will omit the refracted ray and only use the Fresnel equations for the energy percentage of the reflected ray.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/7/cornellbox_recursive.png">
  <div class="figcaption"><br>Conductor and dielectric spheres in the Cornell box, rendered in 751 ms<br>
  </div>
</div>

# More Scenes

<div class="fig figcenter fighighlight">
  <img src="/post_assets/7/windmill_smooth.png">
  <div class="figcaption"><br>Rendered in 7420 ms<br>
  </div>
</div>

<div class="fig figcenter fighighlight">
  <img src="/post_assets/7/scienceTree_glass.png">
  <div class="figcaption"><br>Rendered in 4138 ms<br>
  </div>
</div>

# Result and Future Work

In the end, I have a basic ray tracer that can handle multiple kinds of materials and triangle intersections. However, without the acceleration structures the process is slow. In the future iterations a Bounding Volume Hierarchy or kD-tree implementation is needed.

# References

“Peter Shirley, Trevor David Black, Steve Hollasch. Ray Tracing in One Weekend.” raytracing.github.io/books/RayTracingInOneWeekend.html

Ahmet Oğuz Akyüz, Lecture Slides from CENG795 Advanced Ray Tracing, Middle East Technical University
