# Voxel to Mesh — O(1) Surface Rendering Pipeline

![Domain](https://img.shields.io/badge/Domain-Graphics%20Rendering-e91e63?style=flat-square)
![Optimization](https://img.shields.io/badge/Compute-O(1)%20Matrix%20Operations-00E676?style=flat-square)

<img width="1272" height="918" alt="image" src="https://github.com/user-attachments/assets/195e73be-6f61-4440-b46f-6cd6284c3240" />

## Overview: The Physics vs. Rendering Gap

In high-density spatial compute architectures (like voxel engines or the U.M.E.R. framework), physics and collisions are calculated using rigid, absolute memory blocks. While this guarantees deterministic, $O(1)$ performance, the raw visual output is inherently "blocky" and jagged. 

Modern game engines (Unity, Unreal) expect smooth polygon meshes (vertices and triangles) for their rasterization pipelines. This repository solves that exact bottleneck. It demonstrates an ultra-fast algorithm to take a raw 3D spatial grid, instantly detect the outer "skin", and generate a smooth rendering mesh without ever relying on expensive neighbor-search loops or tree traversals.

## Core Architectural Logic

The pipeline operates in two distinct, highly optimized phases:

### 1. O(1) Surface Detection (The "Skinning")
To determine which voxels actually touch the air (and therefore need to be rendered), traditional engines loop through every block and check its 6 neighbors—an $O(N)$ operation that chokes CPUs. 
This script utilizes **Parallel Array Shifting**. By padding the 3D tensor and shifting the memory slices in all 6 cardinal directions simultaneously, the surface mask is calculated via a single bitwise operation:
`is_surface = voxel_data & (~neighbors_solid)`
This resolves the surface boundary of the entire spatial universe instantaneously.

### 2. Midpoint Surface Net Meshing
Instead of utilizing expensive algorithms like Marching Cubes, this pipeline uses a streamlined Midpoint Mesh logic. 
* Every surface voxel's absolute center becomes a 3D Vertex.
* The algorithm scans for 2x2 voxel neighbor patches along the X, Y, and Z planes.
* It connects these midpoints into Quads, which are immediately split into two Triangles. 

## The Output
The final output takes a mathematically rigid, jagged voxel sphere (representing the underlying spatial physics) and extracts a lightweight, smooth `cyan` polygon mesh ready for shading, lighting, and rendering.

## Running the Engine

The logic and 3D Matplotlib visualization are housed inside `game-rendering-fixed.ipynb`.

### Prerequisites
* Python 3.10+
* `numpy` (For the core matrix operations)
* `matplotlib` (For the 3D spatial plotting)

### Execution
Execute the notebook to generate the Voxel Universe, run the surface detection, and render the comparative plots:
```bash
jupyter notebook game-rendering-fixed.ipynb
