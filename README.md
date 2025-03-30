# Q3

# Ray Tracer - Assignment 3 (Antialiasing)

This project extends the ray tracer from Assignment 2 by implementing **antialiasing** through **supersampling**. Each pixel is sampled multiple times using randomly jittered rays, and the final color is averaged to reduce jagged edges and improve image quality.

---

## Table of Contents

1. [Overview](#overview)  
2. [Implemented Features](#implemented-features)  
3. [Project Structure](#project-structure)  
4. [Build and Run Instructions](#build-and-run-instructions)  
5. [Key Code Highlights](#key-code-highlights)  

---

## Overview

This assignment focuses on improving visual fidelity by applying antialiasing techniques. Specifically:

- Multiple rays are cast per pixel (64 in total)
- Random sampling (Monte Carlo) is used within each pixel's area
- The final color is the average of all samples
- **Gamma correction (γ = 2.2)** is still applied after averaging

The result is a smoother image with significantly reduced aliasing artifacts, especially around object boundaries.

---

## Implemented Features

-  Ray tracing with perspective projection  
-  Plane and spheres with Phong shading and shadow computation  
-  Gamma correction  
-  **Antialiasing with 64 jittered samples per pixel**  
-  Image display using OpenGL and GLFW

---

## Project Structure

- **Main_Q3.cpp**  
  Implements the main loop, rendering function, and antialiasing logic using jittered sampling.

- **Scene.h**  
  Contains scene geometry, intersection logic, Phong shading, and shadow handling.

- **Camera.h**, **Ray.h**, **Sphere.h**, **Plane.h**, **Material.h**  
  Same as in previous assignments – used to define geometry, materials, and ray generation.

---

## Build and Run Instructions

### Requirements

- Visual Studio 2022  
- Windows 11  
- GLFW, GLEW, and GLM libraries  
- OpenGL support

### How to Run

1. Open the solution in Visual Studio or create a new project and add all `.cpp` and `.h` files.
2. Link the required libraries: GLFW, GLEW, and GLM.
3. Build the project (`F7`) and run it (`F5`).
4. A window will open displaying the antialiased ray-traced image.

---

## Key Code Highlights

In the `render()` function, each pixel is sampled **64 times**:

```cpp
int N = 64;
float invN = 1.0f / float(N);

// Random number generator for jittering
std::random_device rd;
std::mt19937 gen(rd());
std::uniform_real_distribution<float> dis(0.0f, 1.0f);

for (int j = 0; j < Height; ++j) {
    for (int i = 0; i < Width; ++i) {
        glm::vec3 colorSum(0.0f);

        for (int sample = 0; sample < N; sample++) {
            float rx = dis(gen); // random offset in x
            float ry = dis(gen); // random offset in y
            float px = i + rx;
            float py = j + ry;

            Ray ray = camera.generateRay(px, py, Width, Height);
            glm::vec3 sampleColor(0.0f);
            float t;
            if (scene.intersect(ray, t, sampleColor)) {
                colorSum += sampleColor;
            }
        }

        glm::vec3 color = colorSum * invN;

        // Gamma correction
        color = glm::clamp(color, 0.0f, 1.0f);
        color = glm::pow(color, glm::vec3(1.0f / gamma));

        OutputImage.push_back(color.r);
        OutputImage.push_back(color.g);
        OutputImage.push_back(color.b);
    }
}
