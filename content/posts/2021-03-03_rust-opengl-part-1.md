+++
title = "Learning OpenGL with Rust part 1 (setup)"
date = 2021-03-03
+++

## Note

Validate shaders before assuming a bug elsewhere:

```
glslangValidator myshader.frag
```

## Overview

This is written around [gl33](https://crates.io/crates/gl33) and
[glutin](https://crates.io/crates/glutin).

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
    .with_vsync(true)
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
const VERTICES: [f32; 6] = [
    -0.5, -0.5, 0.0,
    -0.5, 0.5,  0.0,
     0.5, -0.5, 0.0,
];

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


## Shaders

Shaders are small program that run on the GPU.

To be able to display anything we need at least a vertex shader and a fragment
shader.

A simple vertex shader just sets the `gl_Position` to an x, y, z (and w) coordinate.

To add shaders we need to do the following:

1. Have some shader source.
2. Get an id for the shader.
3. Set the shader source and compile it.
4. Create a "shader program".

Shaders does not have to be created whilst either a VBO or VAO is bound.

Note that the following code creates a vertex shader. For a fragment shader
change the word **vertex** to **fragment** everywhere, and for the actual shader
source see further down.

```rust
// Write the shader
let vertex_shader_src = br#"
#version 330 core

layout (location = 0) in vec3 position;

void main() {
    gl_Position = vec4(position, 1.0);
}
"#;
```

To create an empty shader to associate the shader source with:

```rust
let vertex_shader = glCreateShader(GL_VERTEX_SHADER);
```

Finally set the source and compile it:

```rust
// Set the source
glShaderSource(
    vertex_shader,                      // shader id
    1,                                  // number of shaders
    &vertex_shader_src.as_ptr().cast(), // the shader source
    &(vertex_shader_src.len() as i32)   // the length of the source
);

// Compile the shader
glCompileShader(vertex_shader);
```

To the same thing for the fragment shader, except replace the word vertex with
fragment. 

The shader source for the fragment shader looks slightly different:

```sl
# version 330 core

out vec4 colour;

void main() {
    // return the colour blue
    colour = vec4(0.0, 0.0, 1.0, 1.0);
}
```

Once both shaders are compiled, create a shader program.

```rust
// Create an empty program
let shader_program = glCreateProgram();

// Attach the vertex and fragment shaders
// to the program
glAttachShaders(shader_program, vertex_shader);
glAttachShaders(shader_program, fragment_shader);

// Link the program
glLinkProgram(shader_program);
```

## Shader logging (not required)

While this is not required it can be very helpful.
This can be done when compiling a shader, as well as linking the shader program.

```rust
let mut success = 0;
glGetShaderiv(vertex_shader, GL_COMPILE_STATUS, &mut success); // query shader object

if success == 0 { // anything but zero qualifies as success
    let mut log: Vec<u8> = Vec::with_capacity(1024); // should be enough
    let mut len = 0;
    
    glGetShaderInfoLog(
        vertex_shader,
        log.capacity() as i32,
        &mut len,
        log.as_mut_ptr(),
    );
    unsafe { log.set_len(len) };
    let log = std::str::from_utf8(&log).unwrap();
    eprintln!("Error: {}", log);
}
```

The same applies to the status of the shader program, with some minor
modifications:

Instead of `glGetShaderInfoLog` call `glGetProgramInfoLog` with the
`shader_program` as the first argument rather than `vertex_buffer`.

## Summary

1. [ ]  Create a window
2. [ ]  Create an OpenGL context
3. [ ]  Create vertex array object
4. [ ]  Create at least one vertex buffer object
5. [ ]  Attach attributes to translate vertex data 
   for the shaders
6. [ ] Draw and swap buffers

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

