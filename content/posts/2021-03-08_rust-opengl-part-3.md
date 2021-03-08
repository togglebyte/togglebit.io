+++
title = "Learning OpenGL with Rust part 3 (textures)"
date = 2021-03-08
draft = true
+++

## Set texture properties

Setting texture properties is done via one of the following functions:

```rust
glTexParameterf     // float
glTexParameterfv    // float array
glTexParameteri     // integer
glTexParameteriv    // integer array
glTexParameteru     // unsigned integer
```

