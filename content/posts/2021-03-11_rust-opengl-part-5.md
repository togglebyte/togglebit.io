+++
title = "Learning OpenGL with Rust part 4 (coordinates)"
date = 2021-03-11
+++

## Coordinates

NDC = Normalized Device Coordinates (-1,-1 to 1,1)


## View matrix and projection matrix

The view matrix is where in the world the user is looking.
A view matrix can be considered a point in space.

The view and projection matrix together could be thought of as an inverse
camera, where the view matrix is the cameras location in space, and the
projection matrix represents the zoom / focal length.
