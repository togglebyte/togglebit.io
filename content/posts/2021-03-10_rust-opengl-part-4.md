+++
title = "Learning OpenGL with Rust part 4 (transformations)"
date = 2021-03-10
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


## Scale matrix

Scale / resize the object.

```
┌         ┐
│ X 0 0 0 │
│ 0 Y 0 0 │
│ 0 0 Z 0 │
│ 0 0 0 1 │
└         ┘
```

## Translation matrix

A translation matrix will move the object.

```
┌         ┐
│ 1 0 0 X │
│ 0 1 0 Y │
│ 0 0 1 Z │
│ 0 0 0 1 │
└         ┘
```
