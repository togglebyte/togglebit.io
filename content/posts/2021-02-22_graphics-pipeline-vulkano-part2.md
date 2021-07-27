+++
title = "Vulkano: Graphics pipeline"
date = 2021-02-22
+++

Before reading this, it is useful to have read the [previous post](@/posts/2021-02-18_vulkano-part1.md).

## Vertex

A vertex can be any struct that has two or three points, as long as it
implements `Copy`, `Clone` and `Default`.

```rust
#[Copy, Clone, Default]
struct Vertex {
    position: [f32; 2], // can be the size of three as well, for 3d
}

vulkano::impl_vertex!(Vertex, position);
```

A vertex buffer is just another buffer, eg a `CpuAccessibleBuffer`.

## Render pass

Example of a simple render pass, supplying image format and no multi sampling:

```rust
let render_pass = Arc::new(vulkano::single_pass_renderpass!(device.clone(),
    attachments: {
        color: {
            load: Clear,  // clear the image when entering the render pass.
            store: Store, // use Store here, if the image isn't temporary and
                          // only used inside the render pass, in which case:
                          // use store: DontCare
            format: Format::R8G8B8A8Unorm,
            samples: 1,
        }
    },
    pass: {
        color: [color],
        depth_stencil: {}
    }
).unwrap());

```

In this example the first occurrence of `color` is an arbitrary name, which has
to match the value in the array given to `color` inside `pass`.

It is possible to have an arbitrary number of attachments.
Each added attachment should be included in the `color` section of the `pass`.

Any operation outside of the draw call has to happen after `end_render_pass`,

## Framebuffer

## Swapchain

A `Swapchain` is a group of images.

A Swapchain with two images would sometimes be referred to
as a double buffer.

Use the swapchain to draw to one of its images while the other is being
presented.

## Windows

Create a window as a surface:

```rust
use vulkano_win::VkSurfaceBuild;
use winit::EventLoop;
use winit::WindowBuilder;

let events_loop = EventLoop::new();
let surface = WindowBuilder::new().build_vk_surface(&events_loop, instance.clone()).unwrap();
```

Recreate everything that depends on the window size every time the window is
resized. For instance the swap chain.
