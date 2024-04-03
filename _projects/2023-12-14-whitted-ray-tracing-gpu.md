---
layout: project
title:  Whitted ray tracing using GPU acceleration
repository: https://github.com/paoloMaldonado/RayTracing_gpu
tools: CUDA, C++, OpenGL
kind: Undergraduate Thesis
---

![Imgur](../images/projects/ray-tracing-gpu/ray-tracing-gpu@4x-8.png)

I implemented ray tracing entirely on a GPU based on Whitted's algorithm, with the goal of rendering fully ray-traced scenes in real-time settings through a user interface. This project served as my undergraduate thesis. The implementation covers ray tracing for perfect light reflection and refraction, as outlined in the [original paper](https://www.cs.drexel.edu/~deb39/Classes/Papers/p343-whitted.pdf). The algorithm is capable of reproducing light effects such as mirror reflection and diffuse reflection. Additionally, I implemented five Physically Based Rendering (PBR) materials using a design pattern based on the [PBRT book](https://pbrt.org/). The implementation also includes linear transformations, scene loading, and real-time configuration. Users can explore and experiment with scenes, adjusting settings such as the number of bounces, light, and camera position. 

<!-- more -->

## Motivation
This project idea was born as an extension of a previous personal project, which involved a much simpler ray tracing implementation on the CPU. Around the time I was enrolled in the thesis course, I began learning the fundamentals of CUDA, understanding the workings of GPUs, and delving into the basics of rendering theory. I thought that it could be a good idea to apply all that new knowledge in practice by extending my previous project into a more advanced ray tracing implementation, this time on the GPU, and with a more organized codebase (ok, still a long way to go). The objective was to personally assess how scenes with hundreds of triangles could be rendered in just a couple of milliseconds, in contrast to my earlier CPU-based implementation.

## Tools and dependencies
### Programming languages
- C++
- CUDA-C++

### Dependencies
- [GLFW](https://www.glfw.org/): Window and input management (keyboard and mouse)
- [Dear ImGui](https://github.com/ocornut/imgui): Graphical interface
- [Glad](https://glad.dav1d.de/): Loading OpenGL functions
- OpenGL: Displaying the rendered image continuously
- [tinyobjloader](https://github.com/tinyobjloader/tinyobjloader): OBJ loader and triangulation

## Features
These are the functionalities I have implemented in my application. Each subtitle briefly explains the concept and outlines the implementation:

### Support for Wavefront .OBJ files and .MTL files
My raytracer supports the loading of .OBJ files, extracting vertex positions and vertex normals. Currently, texture mapping is not implemented, and therefore, texture coordinates loading is not supported yet. Additionally, it reads .MTL files, though in a customized format. I modified the illumination model **_illum_** parameter; so now the parameter ranges from 0 to 3. The models are as follows:

- 0: Mirror
- 1: Glass
- 2: Matte
- 3: Plastic

The application also supports the rendering of spheres. These primitives were initially used in the early stages of development to test all functionalities before implementing the Triangle primitive.

### PBR Materials
The materials I implemented are listed in the table below, which also includes the BxDFs that compose each material. BxDF stands for a general form of the terms BRDF (Bidirectional Reflection Distribution Function) for specular reflection and BTDF (Bidirectional Transmission Distribution Function) for specular refraction. The implementation of this concept was based on Chapter 8 of the PBRT book. I followed the design pattern for the implementation of the BxDF base class, from which each specific BxDF was derived. In total, five BxDFs were implemented, and the materials are composed of one or more of these BxDFs. Specular reflection and Refraction implement the fresnel factor in conjunction with the Snell's law for a more realistic output.

BxDFs:

| BxDF                             | Class name             |
| -------------------------------- | -----------------------|
| Lambertian\*                     | _LambertianReflection_ |
| Phong\*                          | _PhongReflection_      |
| Blinn-Phong\*                    | _BlinnPhongReflection_ |
| Specular Reflection              | _SpecularReflection_   |
| Specular transmission/refraction | _SpecularRefraction_   |

\*Requires a normalization factor, these concepts were taken from [\[1\]](https://users.tricity.wsu.edu/~bobl/personal/mypubs/1993_plausible.pdf), [\[2\]](http://www.thetenthplanet.de/archives/255), [\[3\]](https://www.cs.princeton.edu/courses/archive/fall03/cs526/papers/lafortune94.pdf).

Materials:

| Material | Class name        | BxDF(s) composition              |
| -------- | ----------------- | -------------------------------- |
| Matte    | _MatteMaterial_   | Lambertian                       |
| Glass    | _GlassMaterial_   | Specular Reflection & Refraction |
| Plastic  | _PlasticMaterial_ | Lambertian & Phong/Blinn-Phong   |
| Mirror   | _MirrorMaterial_  | Specular Reflection              |

## Results
Some quantifiable results are listed in the table below. I used various variations of the Cornell Box scene for testing because the bounces of light are more noticeable in these scenes. Moreover, the Utah Teapot and the Stanford Bunny were also rendered. We noticed that the rendering resolution and the number of bounces greatly affect the rendering time. Additionally, my application renders objects with up to \~5k triangles while still maintaining an 'interactive' framerate, although at low resolutions. I made minor tweaks to the program, but the issue I found to have the most significant impact is the process of 'naive' intersection. This problem is better addressed using a spatial acceleration structure, which is part of a future step.

| Scene name          | Resolution | FPS  | Miliseconds (ms) |
| ------------------- | ---------- | ---- | ---------------- |
| CornellBox-Original | 1024x576   | 80.3 | 12.45            |
| CornellBox-Sphere   | 1024x576   | 3.1  | 321.101          |





