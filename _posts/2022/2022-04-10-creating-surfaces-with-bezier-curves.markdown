---
layout: post
title: Creating surfaces with bezier curves
date: '2022-04-10 13:00'
excerpt: Here we will create surfaces with patches on OpenGL 4.6 with animations.
comments: true
image: /post_assets/0/post_image.png
published: false
---


In this project, the aim is to create a surface from scratch with bezier curve equations. This will allow us to create animations simply by changing the control points of the surfe we want to create. I will do the computations on the CPU then send the mesh to the GPU with a texture, so this process is not optimal in any means. I used OpenGL 4.6 to implement the project.

## Idea behind the bezier curves and surfaces

To mimic the nature in Computer Graphics field, we need some functionality to represent smooth shapes. There are many algorithms to do it, but they need to have some properties to be useful. They need to be fast to compute and can be easily designed to model various things. 

A good and balanced way is to use cubic polynomials. Many different usage of these polynomials by their constraints leads to diffetent kinds of curves. The most known versions are, Bezier curves, Hermite curves and splines. The main focus of this project will be Bezier curves.

Bezier curves are defined with 4 points, the first and the last one are the start and end points of our curve. The intermediate ones are for the direction of the curve in between steps. With these ponts and linearly interpolating multiple lines, we will have our Bezier curve. The curve is defined in the convex hull of these control points. This property can help us in some applications like robotics, or game development where we need to estimate the boundaries of our path without calculating the whole curve.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/0/bezier.gif">
  <div class="figcaption"><br> Interpolation of the Bezier curve.<br>
  </div>
</div>

<div class="fig figcenter fighighlight">
  <img src="/post_assets/0/blending-functions-bezier.png">
  <div class="figcaption"><br> Blending function of Bezier curves which shows the effect of the control points through the curve.<br>
  </div>
</div>

## How to create a bezier surface

We had our curve in our hand, to make it a surface we need to add a new dimension to our curve. If we define our control points of our curve as another Bezier curves, if we sample these infinite curve cluster with a wanted rate we will have a point cloud which represents a surface.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/0/bezier_surface.png">
  <div class="figcaption"><br> <br>
  </div>
</div>

In a way, we have 4 curves in each direction which spawns from the control points of the curve. This means with total 16 points, we can define a bezier surface which can be easily adjustable for our needs.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/0/bezier_surface_control_points.png">
  <div class="figcaption"><br> <br>
  </div>
</div>

I started my implementation with defining these surface equations as matrix calculations. With these matrices we can easily sample our points in wanted intervals. I created three different matrices for each dimension `(x,y,z)` with 16 different control points. After that, I defined Bezier curves base matrix which comes from the wanted control points interpolation in polynomials. Then with the following equations we can sample our points through the two directions which we created our curves on in the interval `u x w = [0,1]x[0,1]`. 

<div class="fig figcenter fighighlight">
  <img src="/post_assets/0/bezier_equations.png">
  <div class="figcaption"><br> Equations for the bezier curve with matrices. <br>
  </div>
</div>

After creating matrices, I simply iterated with wanted sample counts, and I weave them as triangles. I used a basic normal calculation in vertices which averages the neighbour faces normals in the end. 

<div class="fig figcenter fighighlight">
  <img src="/post_assets/0/my_first_surface.png">
  <div class="figcaption"><br> My first implementation with 10 sample points.<br>
  </div>
</div>

## How to create patches with G1 continuity 

## The problem of normals

## Debugging a OpenGL program with RenderDoc

## Final words and future work


