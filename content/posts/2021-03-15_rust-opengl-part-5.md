+++
title = "Learning OpenGL with Rust part 5 (instanced)"
date = 2021-03-15
+++

## Overview

Transforming instances when using instanced rendering doesn't work with
`uniform`s  as each instance needs its own transform.

**Note**: It is possible to pass an array of `uniform`s per draw call.
It would be more performant to pass 100 transforms as an array to a single
uniform, rather than 100 transforms as vertex pointers.

Iterate over the instance positions and set them via `glBufferData` (which
replaces the way of doing it by setting the uniform value).

## Shader

Previously the shader had a `uniform` value called "transform" that was updated
for every instance, and every instance was drawn using `glDrawArrays`.

With instanced rendering this changes to an input variable, since each instance
will have its transform added to a `Vec<Matrix4<f32>>` and finally written using
`glBufferData`.

```glsl
#version 330 core

layout (location = 0) in vec3 position;
layout (location = 1) in vec2 uv_coords;
layout (location = 3) in mat4 transform; // <-- new

uniform mat4 vp; // formerly transform

out vec2 tex_coords;

void main() {
    gl_Position = vp * transform * vec4(position, 1.0);
    tex_coords = uv_coords;
}
```

## Setting up the VBOs

A `VBO` was used to setup the quad and write the vertex data to the gpu.

Create another `VBO`, this time adding the `glVertexAttribPointer` for the input
paramter `transform` in the shader.

The caveat here is that it's not possible to add `mat4` as a vertex attribute,
as the biggest value is `vec4`. However it is possible to split it into 4 x
`vec4`:

```rust
let mut vbo = 0;
glGenBuffers(1, &mut vbo);
glBindBuffer(GL_ARRAY_BUFFER, vbo.0);

// Size of a vec4
let attrib_size = 4 * size_of::<f32>();

// Size of a mat4
let mat_size = (4 * attrib_size) as i32;

let transpose = false as u8;

// The transform
glVertexAttribPointer(
    3,          // Location (first starts at 3)
    4,          // Size (four floats = one vec4)
    GL_FLOAT,   // Data type
    transpose,
    mat_size,
    (0 * attrib_size) as *const _,
);
// ... Repeat three more times
glVertexAttribPointer(
    6,
    4,
    GL_FLOAT,
    transpose,
    mat_size,
    (3 * attrib_size) as *const _,
);


// Enable all four vertex attributes
// that makes up the entire `mat4`
glEnableVertexAttribArray(3);
glEnableVertexAttribArray(4);
glEnableVertexAttribArray(5);
glEnableVertexAttribArray(6);

glVertexAttribDivisor(3, 1);
glVertexAttribDivisor(4, 1);
glVertexAttribDivisor(5, 1);
glVertexAttribDivisor(6, 1);
```

## Drawing

During the rendering process create a `Vec<Matrix<f32>>` of all the positions,
then load the data with

```rust
const QUAD: [Vertex; 4] = ...;

glDrawArraysInstanced(
    GL_TRIANGLE_STRIP,
    0,
    QUAD.len() as i32,
    number_of_instances,
);

// .. View projection etc.

context.swap_buffers();
```
