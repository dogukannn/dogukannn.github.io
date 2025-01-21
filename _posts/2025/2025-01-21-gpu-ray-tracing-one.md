---
layout: post
published: true
title: Monte Carlo Path Tracing With DXR - Introduction and Basic Optimization Techniques
date: '2025-01-21 12:00'
image: /post_assets/13/post_image.png
excerpt: Here we will implement monte carlo path tracing with DXR on the GPU.
comments: true
share-img: /post_assets/13/post_image.png
---

In this post, I will introduce the DXR through porting my CPU based ray tracer into the GPU. The main goal is to implement a monte carlo path tracer with DXR on the GPU. From the post you can see that for the begining of this series, most of time is spent on the setup and the initial implementation. In the next posts, I will focus on missing features for a rendering engine and I want to create a rendering suite with both CPU and GPU implementations to compore and further investigate the techniques of path tracing like subsurface scattering, volumetric rendering, and more. 

For the code and the project files, you can check [my repository on GitHub](https://github.com/dogukannn/dxr-pathtracer).

# Initial Setup

# Defining Shaders

# Creating the Pipeline

## Calculating Shader Offset For Each Intersected Object

# Creating Acceleration Structures

## Multi Mesh Bottom Level Structures

# Passing Data to the GPU

## Instance Buffers

## Local and Global Root Arguments

## HLSL Buffer Alignment Issues With Arrays

# Ray Generation

# Closest Hit Shaders

## Importance Sampling

## Shadow Ray Hack

## Issues With Random Float Generation and Hash Functions

## Next Event Estimation

## Russian Roulette

## Splitting

## Ray Flags For Refraction

# Miss Shaders

# Future Optimizations for Real Time Rendering

# Results

# References

Ahmet Oğuz Akyüz, Lecture Slides from CENG795 Advanced Ray Tracing, Middle East Technical University

