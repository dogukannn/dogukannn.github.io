---
layout: post
published: true
title: Geodesic Distances and Shape Descriptors
excerpt: Here we will examine 3D meshes with geodesic distances and shape descriptors.
date: '2022-04-11 13:00'
comments: true
---

The geodesic distance is the distance between 2 points along the surface of the object.  In this project we will examine the ways to find them and see the ways to use these distances. Finally, we will deep dive on shape descriptor algorithms like iso-curve signature and bilateral maps.

## Idea behind Geodesic Distances

In the area of digital geometry processing, geodesic distances in 3D meshes helps us a lot in lots of fields because of it's properties like being invariant to different poses opposed to the euclidean distances.

In 3D world when we want to calculte the distance between two vertices, we will simply find the vector between them, and get the magnitude of it. This will result in a Euclidean distance, we can imagine it like a line which connects these vertices. However, this distance is not much usefull if we ever want to inspect the meshes. The distance between vertices will change with the pose and scale of the mesh. We need something invariant to these effects. We can think of it like our height doesn't change when we sit down. Luckily, we have a measurement which is called geodesic distance.

We can think a 3D mesh as an undirected weighted graph. In this graph weight of edges are the distances between vertices. If we find the shortest path between two vertices the distance of the path will be the geodesic distance between these vertices. If we paint these path on our mesh we can clearly see that our path is on the surface. 

<div class="fig figcenter fighighlight">
  <img src="/post_assets/1/geodesic_example.png">
  <div class="figcaption"><br> Red line is geodesic distance and the blue line is the euclidean distance.<br>
  </div>
</div>

## Ways to find Geodesic Distances between vertices

## A glance to Dijkstra's shortest path algorithm

## Using Geodesic Distances

## Shape Descriptors 

## Geodesic Iso-Curve Signatures

## Bilateral Maps

## Final words

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
