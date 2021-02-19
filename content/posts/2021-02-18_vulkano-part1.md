+++
title = "Vulkano: setup"
date = 2021-02-18
+++

## Setup:

```
[dependencies]
vulkano = "0.20.0"
vulkano-shaders = "0.20.0"
```

Before anything can be done, create an `Instance`, which is the entry point to
the Vulkan library.

Next select a `PhysicalDevice`, and finally create a `Device` which represents
a channel to the physical device.

```rust
use vulkano::instance::{Instance, InstanceExtensions, PhysicalDevice};

let instance = Instance::new(None, &InstanceExtensions::none(), None).unwrap();

// get the first available physical device
let physical_device = PhysicalDevice::enumerate(&instance).next().unwrap();
```

To perform an operation it has to be submitted to a queue, and a queue family
has to be selected before a `Device` can be created.

```rust
let queue_family = physical_device.queue_families()
    .find(|q| q.supports_graphics())
    .unwrap();

```

Finally a `Device` along with a `Queue` can be created.

```rust
use vulkano::device::{Device, DeviceExtensions, Features};

let priority = 0.5; // between 0.0 and 1.0
let dev_extensions = DeviceExtensions {
    // This is required for the compute shaders to work
    khr_storage_buffer_storage_class: true,
    ..DeviceExtensions::none()
};

let (device, mut queues) = Device::new(
    physical_device,
    &Features::none(),
    &dev_extensions,
    [(queue_family, priority)].iter().cloned()
).unwrap();

let queue = queue.next().unwrap();
```

## Buffers

A buffer is used to share data with the gpu. Buffers can be created from either
an iterator or data using either # `CpuAccessibleBuffer::from_iter` or
`CpuAccessibleBuffer::from_data`.

Note that the data has to be laid out correctly.
Variables in GLSL 

The `CpuAccessibleBuffer` can be used to both read and write data to and from
the gpu.

```rust
use vulkano::buffer::{BufferUsage, CpuAccessibleBuffer};

let data = 123;
let buffer = CpuAccessibleBuffer::from_data(
    device.clone(),
    BufferUsage::all(),
    false,
    data,
).unwrap()
```

## Command buffers

Unlike a buffer that stores data, the command buffer holds commands that will be
executed by the GPU.

It's not possible to execute a command directly on the GPU so an `AutoCommandBuffer`
is required.

Following code will copy the values from the `src` to the `dst` buffer.

```rust
use vulkano::command_buffer::{CommandBuffer, AutoCommandBufferBuilder};
use vulkano::sync::GpuFuture;

let src = CpuAccessibleBuffer::from_iter(
    device.clone(),
    BufferUsage::all(),
    false,
    0..10,
).unwrap()

let dst = CpuAccessibleBuffer::from_iter(
    device.clone(),
    BufferUsage::all(),
    false,
    (0..10).map(|_| 0),
).unwrap()

let mut builder = AutoCommandBufferBuilder::new(
    device.clone(),
    queue.family()
).unwrap();

// Command to execute: copy_buffer
builder.copy_buffer(src.clone(), dst.clone()).unwrap();
let command_buf = builder.build().unwrap();

// Execute the command
let finished = command_buf.execute(queue.clone()).unwrap();

// Drive the command to completion.
// Without this the copy won't happen.
finish
    .then_signal_fence_and_flush()
    .unwrap()
    .wait(None)
    .unwrap();

// Data now contains the values from src, 
// read out of the dst buffer
let data = *dst.read().unwrap();
```

### Sources

* [http://vulkano.rs/guide/](http://vulkano.rs/guide/)
* [https://docs.rs/vulkano/0.20.0](https://docs.rs/vulkano/0.20.0)
* [https://vulkan-tutorial.com/](https://vulkan-tutorial.com/)
