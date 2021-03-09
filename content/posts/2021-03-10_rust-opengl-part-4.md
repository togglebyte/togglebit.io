+++
title = "Learning OpenGL with Rust part 4 (transformations)"
date = 2021-03-09
draft = true
+++

## Transformation matrix

Identity matrix

```
┌         ┐
│ 1 0 0 0 │
│ 0 1 0 0 │
│ 0 0 1 0 │
│ 0 0 0 1 │
└         ┘
```

To apply all transformations to a transformation (e.g moving, scaling and
rotating an object), build up the matrix by applying each transformation
individually to the identity matrix.

The order in which these matrices are applied is important:

Translating an object away from the origin, and the applying rotation, will then
rotate the object around the origin in a wide arc, as opposed to applying
rotation first, meaning the object will rotate around it self before moving.
