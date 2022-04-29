---
layout: post
published: false
title: Catmull–Clark and √3 Subdivision Algorithms
---
Subdivision algorithms generate new meshes by dividing the faces and changing the locations of the vertices from the base polygonal meshes. It can preserve or smooth out the current shape of the meshes. The algorithms without changing the vertex locations exists, but they are less common. The main examples are Loop subdivision, Doo-Sabin subdivision, Catmull-Clark subdivision and 3 subdivision. In this project, we will see the Catmull-Clark and 3 subdivision and see the differences between them with examples implemented in C++ with the help of the OpenGL.

## Where can subdivision algorithms be used? 

It is primarily used in areas like visual effects, 3D modeling and computer animations. The achieved smoothing after the algorithms can speed up the process for creating a new smooth mesh from scratch. In the smoother versions of the meshes, because of the face count, the lighting and rendering will be much more realistic without losing the shapes look much. These differences in shading can be seen in the examples on the next sections. 

## Quad Meshes

For this project, we will see the Catmull-Clark algorithm which originally processes quad meshes only. However, the modern OpenGL only works with triangles in order to maintain performance because of the modern GPU architectures. 

To solve this issue I created a Quad Mesh class which is operating on quads in the processing level, but turns quads into two triangles for the GPU. During the implementations I wanted to achieve a flat shading effect to show the faces during each iteration. For the quad meshes, to see this effect I give more than one normals which is not the case in default settings. For, example we can define a cube with 8 vertices and 12 triangles. In my implementation every vertex have different normals for the face they are part of. This way I can achieve the beatiful look in edges. 

## Catmull-Clark Subdivision

The Catmull-Clark algorithm creates surfaces recursiveley. The output mesh of the algorithm will only consist of quadritalerals which are not planar with their neigbours. The output will have a smoother look than the base mesh. 

The algorithm simply defined with 4 different iterations on the elements of the base mesh which are vertices, edges and faces. 

- In the beginning, for each face a face point created which is the center of the mass of the face. 
- In the second iteration, for each edge an edge point is created which is the average of the neighbour faces face points and edges center point. 
- In the next iteration, the vertex positions are updated according to following formula,
	
// INSERT FORMULA PNG

The d are the original vertices coordinates. R means the average of the neighbour edge points, and S means the average of the neighbour face points. The result will be a formulated weighted average of these points. 

- In the final iteration, for each old face we define 4 new faces with the order like (old vertex, edge point, face point, edge point). For each old vertex we will have a new quad and we discard the old faces.


In the end we will left with a smoother new mesh which consists 3 times more quads than the base mesh.

// INSERT EXAMPLE IMAGES

We can see the cube turns into a sphere like shape. However, it is not a perfect square because it can be experessed quadratic polynomials. 

We can see the total area is steadily decreasing as the shape turns into a smoother one.

## Problem with holes

While working on the algorithm, I notices some shapes are giving some weird results. For example the heart mesh is generating holes after the iterations. After some debugging I noticed that the given mesh points is not fully constructing a perfect two manifold mesh. This causes to algorithm thinking the the edges which are not supposed to be hole edges as holes. 


// INSERT HEART WITH HOLE

To solve this problem I implemented a check whether the vertex is on a hole edge or not. If the vertex on the hole edge, I calculated its positions such that it doesn't generate holes after the iterations.

// INSERT HEART WITHOUT HOLE

However, as seen in the picture the hole edges can't be smoothed because of the wrong neighbour informations after my solution. In the end I think not creating holes after iterations is generating more plausible meshes. 


## √3 Subdivision

## Final Notes

## References
