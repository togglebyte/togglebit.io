+++
title = "Vulkano: setup"
date = 2021-02-18
+++

This are incomplete notes.

## Overview

Buffers are used to read and write data.

Device is channel to a physical device (gpu).

Command buffer executes all commands in one go, for efficiency.

## Setup

```toml
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

## Compute shader

A shader is a program that runs on the gpu. Shaders are written in GLSL and
compiled to SPIR-V using `vulkano-shaders` in this instance.

The following shader has two descriptors: `Data` and `MoreData`.
The `binding` represents the order the buffers was added.

```rust
mod cs {
    vulkano_shaders::shader! {
        ty: "compute",
        src: "
# version 450

layout(local_size_x = 1, local_size_y = 1, local_size_z = 1) in;

layout(set = 0, binding = 0) buffer Data {
    uint value;
} data;

layout(set = 0, binding = 1) buffer MoreData {
    uint value;
} more_data;

void main() {
    data.value = 123;
    mode_data.value = 234;
}

"
    }
}
```

```rust
struct Data { val: u32 }
struct MoreData { val: u32 }

fn main() {
    // .. code from earlier
    use vulkan::pipeline::ComputePipeline;
    use vulkan::descriptor::PipelineLayuotAbstract;
    use vulkan::descriptor::descriptor_set::PersistentDescriptorSet;

    let data_buffer = CpuAccessibleBuffer::from_data(
        device.clone(),
        BufferUsage::all(),
        false, // don't cache data on the cpu
        Data { val: 0 }
    );

    let more_data_buffer = CpuAccessibleBuffer::from_data(
        device.clone(),
        BufferUsage::all(),
        false, // don't cache data on the cpu
        MoreData { val: 0 }
    );

    // load shader module
    let shader = cs::Shader::load(device.clone()).unwrap();

    let pipeline = Arc::new(ComputePipeline::new(
        device.clone(),
        &shader.main_entry_point(),
        &(),    // SpecializationConstants
        None,   // Cache
    ));

    let layout = compute_pipeline.layout().descriptor_set_layout(0).unwrap();

    // note that all sets will be present at the time of execution (of the shader)
    // the set in the compute shader in this example is zero
    let set = Arc::new(
        PersistentDescriptorSet::start(layout.clone())
            .add_buffer(data_buffer.clone()).unwrap()
            .add_buffer(more_data_buffer.clone()).unwrap()
            .build().unwrap()
    );

    let workgrup = [1, 1, 1]; // x, y and z
    let builder = AutoCommandBufferBuilder::new(device.clone(), queue.family()).unwrap();
    builder.dispatch(workgroup, pipeline.clone(), set.clone(), ()).unwrap();
    let command_buffer = builder.build().unwrap();

    // execute compute shader
    let result = command_buffer.execute(queue.clone()).unwrap();
    result.then_signal_fence_and_flush().unwrap().wait(None).unwrap();

    // access the data
    let buffer_data = &*buffer.read().unwrap();
}

```

### Sources

* [http://vulkano.rs/guide/introduction](http://vulkano.rs/guide/introduction)
* [https://docs.rs/vulkano/0.20.0](https://docs.rs/vulkano/0.20.0)
* [https://vulkan-tutorial.com/](https://vulkan-tutorial.com/)
