+++
title = "Learning OpenGL with Rust part 1"
date = 2021-03-03
+++

## Note

Validate shaders before assuming a bug elsewhere...

## Overview

This is written around [gl33](https://crates.io/crates/gl33) and
[winit](https://crates.io/crates/winit).

OpenGL coordinates places 0,0 at the bottom left.

Normalized Device Coordinates (NDC) places 0,0 in the centre of the screen.
Ouput of the vertex shaders is in NDC;

## Window setup

The important part of creating the window handle is the OpenGL context.
There can only be one current context at a time. 

To use another context on another thread, call `make_not_current` on the current
context first, before making the context on another thread current with `make_current`.

```rust
let event_loop = EventLoop::new();
let window_builder = WindowBuilder::new().with_title("a window title");

// Set this to OpenGL 3.3
let context = ContextBuilder::new()
    .with_gl(GlRequest::Specific(Api::OpenGl, (3, 3)))
    .build_windowed(window_builder, &event_loop)
    .unwrap();

let context = unsafe { context.make_current().unwrap() };
```

## Load all OpenGL functions 

Load all OpenGL functions globally:

```rust
unsafe {
    load_global_gl(&|ptr| {
        let c_str = std::ffi::CStr::from_ptr(ptr as *const i8);
        let r_str = c_str.to_str().unwrap();
        context.get_proc_address(r_str) as _
    });
}
```

## Vertex array object

A vertex array object contains a list of vertex buffer object ids, as well as
information on how to interpret the data representing a vertex.

Vertex array objects are required when using OpenGL core.

Note: rendering without the VAO is the "old way" (using `glBegin`, and `glEnd`,
as per OpenGL v1), so it's recommended to have at least one.

```rust
let mut vao = 0u32; // vertex array object
glGenVertexArrays(1, &mut vao); // this creates [one](one) vao
glBindVertexArray(vao);
```

## Vertex buffer object

The vertex buffer object stores the vertices.

Before any operation can be performed on the vertices, a VBO has to be created
and bound:

```rust
let mut vbo = 0u32;
glGenBuffers(1, &mut vbo);
glBindBuffer(GL_ARRAY_BUFFER, vbo);
```

Finally the vertex data can be stored in the buffer:

```rust
glBufferData(
    GL_ARRAY_BUFFER,
    size_of_val(&VERTICES) as isize,
    VERTICES.as_ptr().cast(),
    GL_STATIC_DRAW,
);
```


## Vertex attributes

Vertex attributes describes how the vertex data should be interpreted by the
shaders.

```rust
let vertex_size = size_of::<Vertex>() as i32;
// Define the attribute
glVertexAttribPointer(
    0,            // (location = 0) in the shader
    3,            // number of values, 3 floats in this case
    GL_FLOAT,     // type (f32)
    0,            // bool: normalize or not
    vertex_size,  // stride (total size in bytes of a vertex, in this case)
    0 as *const _ // offset to the first byte of the value
);
glVertexAttribPointer(1, 3, GL_FLOAT, 0, vertex_size, (3*size_of::<f32>()) as *const _);

// Enable the attribute
glEnableVertexAttribArray(0);
glEnableVertexAttribArray(1);
```

```sl
#version 330 core

layout (location = 0) in vec3 position;
layout (location = 1) in vec3 colours;
out vec3 ci;

void main() {
    gl_Position = vec4(position.x, position.y, position.z, 1.0);
    ci = colours;
}
```


## Resources:

* [https://github.com/rust-tutorials/learn-opengl](https://github.com/rust-tutorials/learn-opengl) Learn OpenGL lessons in Rust.
* [https://github.com/Lokathor/gl33](https://github.com/Lokathor/gl33) OpenGL
  bindings for Rust
* [https://learnopengl.com/](https://learnopengl.com/)

## Crates:

* [https://crates.io/crates/glutin](https://crates.io/crates/glutin) Glutin
  (window handling and OpenGL Context)
* [https://crates.io/crates/gl33](https://crates.io/crates/gl33) Gl33 (OpenGL bindings)

## Dictionary

MRT = multiple render target (said mystran)

