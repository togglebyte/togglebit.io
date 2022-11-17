+++
title = "Learning OpenGL with Rust part 4 (transformations)"
date = 2021-03-10
draft = true
+++

## Transformation matrix

Identity matrix

```txt
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

```txt
┌         ┐
│ X 0 0 0 │
│ 0 Y 0 0 │
│ 0 0 Z 0 │
│ 0 0 0 1 │
└         ┘
```

## Translation matrix

A translation matrix will move the object.

```txt
┌         ┐
│ 1 0 0 X │
│ 0 1 0 Y │
│ 0 0 1 Z │
│ 0 0 0 1 │
└         ┘
```

## Coordinates

NDC = Normalized Device Coordinates (-1,-1 to 1,1)

## View matrix and projection matrix

The view matrix is where in the world the user is looking.
A view matrix can be considered a point in space.

The view and projection matrix together could be thought of as an inverse
camera, where the view matrix is the cameras location in space, and the
projection matrix represents the zoom / focal length.

## Putting this together

To get the model matrix we multiply the world matrix with the local coordinates.
However since the local coordinates exists on the mesh as its vertices we don't
need to do that step in Rust.

To render a specific portion of the world, move the `view` matrix.
The coordinates to move the view matrix should be inverse.
So to move the view 10 units to the right, add -10 to the x axis.

```rust
use nalgebra::{Vector, Matrix4};
// Objects transform in the world
let model = Matrix4::<f32>::identity() * world_coords;

let view = Matrix4::identity();

let min_x = -10.0;
let max_x = 10.0;
//... Declare `max` for all other axis
let projection = Matrix4::new_orthographic(
    min_x,
    max_x,
    min_y,
    max_y,
    min_z,
    max_z
);

let clip = projection * view * model
```
