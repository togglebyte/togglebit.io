+++
title = "Learning OpenGL with Rust part 3 (textures)"
date = 2021-03-09
+++

## Creating a texture

First create a texture name (id).
This works the same way as the vertex buffer object, and the vertex array
object, in that we create an id that we bind as the current texture.

```rust
let mut texture_id = 0;
glGenTexture(1, &mut texture_id);
```

Bind the texture using `glBindTexture`.

Any operation performed using `glTextureParameter` will thus apply to the bound
texture.

```rust
glBindTexture(GL_TEXTURE_2D, texture_id);
```

Set the texture properties.
In this case the texture will repeat on both the X and the Y axis if the texture
is smaller than the surface.

```rust
// Set the texture to repeat
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, 0x2901); // 0x2901 = GL_REPEAT
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, 0x2901); // 0x2901 = GL_REPEAT
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, 0x2600); //GL_NEAREST 
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, 0x2600); //GL_NEAREST 
```

Create the texture image with `glTexImage2D`:

```rust
let (dimensions, image_bytes) = get_image();
glTexImage2D(
    GL_TEXTURE_2D,
    0,
    0x1908,  // same as GL_RGBA
    dimensions.width, 
    dimensions.height, 
    0, // Apparently some legacy value
    GL_RGBA,
    GL_UNSIGNED_BYTE,
    image_bytes.as_ptr().cast(),
);
```

## Texture properties

Setting texture properties is done via one of the following functions:

```rust
glTexParameterf     // float
glTexParameterfv    // float array
glTexParameteri     // integer
glTexParameteriv    // integer array
glTexParameteru     // unsigned integer
```

