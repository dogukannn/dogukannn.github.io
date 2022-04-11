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

Thankfully, we have many algorithms to calculate the shortest path between vertices in weighted graphs like Dijkstra and Depth-First Search (DFS) etc. These will provide us an easy way to calculate euclidean distance. However, there is a problem. The shortest path on the surface doesn't need to be on the edges between vertices, it can pass through the faces.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/1/real_shortest_path.png">
  <div class="figcaption"><br> Comparision between different paths.<br>
  </div>
</div>

There are algorithms to better estimate the real shortest path like Fast Marching algortihm, but they are out of scope of this project. 

## A glance to Dijkstra's shortest path algorithm

In this project, we will use Dijkstra's shortest path algorithm to get the geodesic distances. The input of the algorithm is a source vertex, and the algorithm find the distances between all the other vertices. 

In short, the algorithm starts with setting all distances from the source vertex with maximum values and the source vertex with 0. In every iteration, we pick the vertex with least distance. After that, we check wheter the neighbour vertices can be reachable from the vertex we pick with shorter path. In these checks we will save the previous vertices to calculate the shortest path in the end.

In our case we saved our meshes into our memory with structures which represents weighted directed graphs. We will just apply the algorithm to find shortest paths to every other vertex.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/1/q1.png">
  <div class="figcaption"><br> Shortest path drawed on the mesh with red.<br>
  </div>
</div>


## Using Geodesic Distances

The properties of the geodesic distances enable us to use it on applications like sampling, shape descriptors and similarity comparisions. 

In example in the similarity comparisions, we may need to compare meshes with different rotations, deformations and scaling (like mirroring). If we compare the geodesic distances between these meshes we will see less differences from euclidean distances. 

## Shape Descriptors 

## Geodesic Iso-Curve Signatures

## Bilateral Maps

## Final words

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
