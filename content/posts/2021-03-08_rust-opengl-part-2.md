+++
title = "Learning OpenGL with Rust part 2 (shaders)"
date = 2021-03-08
+++

See the [previous post](@/posts/2021-03-03_rust-opengl-part-1.md) for setup
steps and drawing a triangle.

## Logging

```rust
// Get the length of the log data
let mut len = 0;
glGetShaderiv(shader_object, GL_INFO_LOG_LENGTH, &mut len);

// Get the log data
let mut log = Vec::with_capacity(len);
glGetShaderInfoLog(
    shader_object,
    log.capacity() as i32,
    &mut len,
    log.as_mut_ptr()
);

unsafe { log.set_len(len) };

let log_string = std::str::from_utf8(&log).unwarp();
```

## Uniform

A uniform type in a shader is a bit like a global variable.
It exists with the same value for all shaders.

Calling `glGetUniformLocation` on a uniform that is never used will return `-1`
even though the uniform is declared in a shader, as it will be removed if it's
never used.

Declaring and using a uniform in a shader:

```glsl
uniform vec4 colourUniform;
out vec4 colour;

void main() {
    colour = colourUniform;
}
```

To set the uniform value get the location of the specific uniform using
`glGetUniformLocation()`.
Setting the value of the uniform is done in Rust, using `glUniform<type>`.

```rust
let red = 1.0;
let uniform_loc = glGetUniformLocation(shader_program, "colourUniform\0".as_ptr())
glUniform4f(uniform_loc, red, 0.0, 0.0, 1.0);
```

If the uniform location comes back as `-1` it means it wasn't found.
This could be the result of a typo or the fact that the uniform is never used.
