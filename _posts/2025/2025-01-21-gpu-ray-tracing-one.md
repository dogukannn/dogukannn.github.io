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

In this post, I will introduce the DXR through porting my CPU based ray tracer into the GPU. The main goal is to implement a monte carlo path tracer with DXR on the GPU. From the post you can see that for the begining of this series, most of time is spent on the setup and the initial implementation. I will write more about the coding aspect as there are not much tutorials present on the internet. In the next posts, I will focus on missing features for a rendering engine and I want to create a rendering suite with both CPU and GPU implementations to compore and further investigate the techniques of path tracing like subsurface scattering, volumetric rendering, and more. 

For the code and the project files, you can check [my repository on GitHub](https://github.com/dogukannn/dxr-pathtracer).

# Initial Setup

For the initial setup I searched for some DXR examples online and found the [Microsoft's DXR samples](https://learn.microsoft.com/en-us/samples/microsoft/directx-graphics-samples/d3d12-raytracing-samples-win32/), which was a good enough starting point. I picked the basic lighting sample with a cube and a light source. 

The code starts by handling the necessary window operations and DirectX device initialization, this part is same with any other DirectX application (I will skip the swapchain, render target creations etc.). The main difference is the ray tracing pipeline setup and the shader creation. 

In device creation there is one thing we need to add, which is ID3D12Device5 interface. This interface is necessary for ray tracing operations. 

```cpp
ComPtr<ID3D12Device5> m_dxrDevice;
ThrowIfFailed(device->QueryInterface(IID_PPV_ARGS(&m_dxrDevice)), L"Couldn't get DirectX Raytracing interface for the device.\n");
```

After creating the device we can start defining our shaders.

# Defining Shaders

For a raytracing pipeline, we can define 4 new types of shaders: ray generation, closest hit, any hit and miss shaders. However, in this example we won't use any hit shaders, even though they are useful for things like shadow rays, my implementation with closest hit shaders will be easier to understand for the beginning. We can define these shaders as below for a starting point. We will use these shaders to create our pipeline and after that we will fill them with the necessary code.

```hlsl
[shader("raygeneration")]
void MyRaygenShader()
{
    ...
}
[shader("closesthit")]
void MyClosestHitShader(inout RayPayload payload, in MyAttributes attr)
{
    ...
}
[shader("miss")]
void MyMissShader(inout RayPayload payload)
{
    ...
}
```

# Creating the Pipeline

In the raytracing pipeline, unlike the classical pipelines, we have subobjects to define the shaders and the shader binding tables. These are useful for creating a more flexible pipeline, because we don't need to declare every shader or every field to make raytracing pipeline start working. We can get help from DirectX12 helper library (d3dx12.h) to create our pipeline. After creating the pipeline, we will create a library to hold our shaders references which we can use later for the shader binding table.

```cpp
CD3DX12_STATE_OBJECT_DESC raytracingPipeline{ D3D12_STATE_OBJECT_TYPE_RAYTRACING_PIPELINE };
auto lib = raytracingPipeline.CreateSubobject<CD3DX12_DXIL_LIBRARY_SUBOBJECT>();
D3D12_SHADER_BYTECODE libdxil = CD3DX12_SHADER_BYTECODE((void *)g_pRaytracing, ARRAYSIZE(g_pRaytracing)); // g_pRaytracing is the compiled shader code which you can compile with DXC on runtime or offline
lib->SetDXILLibrary(&libdxil);
{
    lib->DefineExport(c_raygenShaderName);
    lib->DefineExport(c_closestHitShaderName);
    lib->DefineExport(c_missShaderName);
}
```

After creating the library, we can define the shader config and the hit group for the pipeline. A hit group is a combination of closest hit, any hit and intersection shaders, miss shaders won't be in the hit groups, as they will be shared across the objects in the scene, but when we dispatch our rays, we can define which miss shader to use. In this sample I will create two different hit groups one for the diffuse materials, and other for the dielectric materials. As a further optimization, we can create a hit group for emissive materials, however in this sample I will use the diffuse hit shader for emissive materials.

```cpp
//like the library, we will define the hit groups as subobjects
auto hitGroup = raytracingPipeline.CreateSubobject<CD3DX12_HIT_GROUP_SUBOBJECT>();
hitGroup->SetClosestHitShaderImport(c_closestHitShaderName);
hitGroup->SetHitGroupExport(c_hitGroupName);
hitGroup->SetHitGroupType(D3D12_HIT_GROUP_TYPE_TRIANGLES);
```

We also need to create the shader config, which will define the payload and attribute sizes for the shaders, here is a nice place to explain these, the payload is the data that will be passed between the shaders, like we can pass the total color values, the recursion depth, or the the distance to the hit etc. The attributes are the data that will be used for intersection data, and for the [fixed-function triangle intersection](https://learn.microsoft.com/en-us/windows/win32/direct3d12/intersection-attributes) we will have the barycentric coordinates of the hitpoint. For procedural primitives, we can define our own attributes and pass them with ReportHit call, which we will call with the our custom defined attributes, which can be maximum 32 bytes. 

```cpp
auto shaderConfig = raytracingPipeline.CreateSubobject<CD3DX12_RAYTRACING_SHADER_CONFIG_SUBOBJECT>();
UINT payloadSize = ...; // size of the custom payload 
UINT attributeSize = sizeof(XMFLOAT2);  // float2 barycentrics
shaderConfig->Config(payloadSize, attributeSize);
```

In the end we will bind our root signature to the pipeline, and create a pipeline config subobject to specify the maximum recursion depth for the raytracing pipeline. 

```cpp
auto globalRootSignature = raytracingPipeline.CreateSubobject<CD3DX12_GLOBAL_ROOT_SIGNATURE_SUBOBJECT>();
globalRootSignature->SetRootSignature(m_raytracingGlobalRootSignature.Get());
auto pipelineConfig = raytracingPipeline.CreateSubobject<CD3DX12_RAYTRACING_PIPELINE_CONFIG_SUBOBJECT>();
UINT maxRecursionDepth = MAX_RECURSION_DEPTH; // This field can help GPU driver to make optimizations for the recursion depth  
pipelineConfig->Config(maxRecursionDepth);
ThrowIfFailed(m_dxrDevice->CreateStateObject(raytracingPipeline, IID_PPV_ARGS(&m_dxrStateObject)), L"Couldn't create DirectX Raytracing state object.\n");
```

A note about the maximum recursion depth is that this value is the absolute maximum value that you can call the TraceRay() function, and the shaders won't stop the recursion for you at this depth, you need to handle this in your shaders. If you don't handle this, you will get a hanged GPU, which results in a device lost error.

## Calculating Shader Offset For Each Intersected Object

As you see we defined every shader that we will use in one pipeline and we will use this pipeline for every object in the scene. The reason behind it is that in ray tracing we won't issue any draw calls for the objects, we will just dispatch rays to the scene and every intersection needs to call the related shader. This calculation is handled by our application and we can define it from multiple locationn, geometry datas for bottom level acceleration structures, instance datas for top level acceleration structures, and as a parameter of the TraceRay() function.

We can think the shader tables as a continuous memory block that holds the shader pointers for the objects in the scene. For each intersection the hit group index will be calculated with the below equation.

```cpp
hitGroupPointer = &hitGroups[0] + (hitGroupStride * (RayTypeOffset + RayTypeStride * GeometryId + InstanceOffset)) 
```

This calculation is done by the DXR API, and quite hard to figure out by yourself, after tackling every variable with slight ressilience, I found a introduction to this topic from [a blog post by Will Usher](https://www.willusher.io/graphics/2019/11/20/the-sbt-three-ways/). The post explains this calculation in depth, and compares the ray tracing frameworks of DXR, OptiX and Vulkan.

<div class="fig figcenter fighighlight">
  <img src="/post_assets/13/sbt_offset.png">
  <div class="figcaption"><br>Figure taken from Will Usher's blog, showing a shader binding table with two instances, the second instance having two different geometries with different values for shader indices<br>
  </div>
</div>

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

