---
title: Introduction to DirectML
description: Direct Machine Learning (DirectML) is a low-level API for machine learning (ML).
ms.topic: article
ms.date: 05/21/2024
---

# DirectML Overview

## Summary

Direct Machine Learning (DirectML) is a low-level API for machine learning (ML). The API has a familiar (native C++, nano-COM) programming interface and workflow in the style of DirectX 12. You can integrate machine learning inferencing workloads into your game, engine, middleware, backend, or other application. DirectML is supported by all DirectX 12-compatible hardware.

Hardware-accelerated machine learning primitives (called operators) are the building blocks of DirectML. From those building blocks, you can develop such machine learning techniques as upscaling, anti-aliasing, and style transfer, to name but a few. Denoising and super-resolution, for example, allow you to achieve impressive raytraced effects with fewer rays per pixel. 

You can integrate machine learning inferencing workloads into your game, engine, middleware, backend, or other application. DirectML has a familiar (native C++, nano-COM) DirectX 12-style programming interface and workflow, and it's supported by all DirectX 12-compatible hardware. For DirectML sample applications, including a sample of a minimal DirectML application, see [DirectML sample applications](dml-min-app.md). Be sure to also check out our [landing page](https://microsoft.github.io/DirectML).

DirectML was introduced in Windows 10, version 1903, and in the corresponding version of the Windows SDK.

## Is DirectML appropriate for my project?

DirectML is a low-level hardware abstraction layer that enables you to run machine learning workloads on any DirectX 12 compatible GPU.  

If you need to optimize your machine learning performance for real-time, high-performance, low-latency, or resource-constrained scenarios, DirectML gives you the most control and flexibility. You can use DirectML to integrate machine learning directly into your existing engine or rendering pipeline, or to build your own custom machine learning frameworks and middleware on Windows.  

You can also use DirectML indirectly through the ONNX Runtime, which is a cross-platform library that supports the open standard ONNX format for machine learning models. The ONNX Runtime can use DirectML as one of its execution providers, along with other backends such as CPU, CUDA, or TensorRT. This way, you can leverage the performance and compatibility of DirectML without writing any DirectML code yourself.  

Alternatively, you can use the WinML API, which is a higher-level, model-focused API that simplifies the machine learning workflow with its load-bind-evaluate pattern. WinML also uses the ONNX format for models, and can use DirectML as its backend. WinML is designed for scenarios where you need to quickly and easily integrate machine learning into your Windows applications, without worrying about the details of the underlying hardware or framework. 


## What work does DirectML do; and what work must *I* do as the developer?

DirectML efficiently executes the individual layers of your inference model on the GPU (or on AI-acceleration cores, if present). Each layer is an operator, and DirectML provides you with a library of low-level, hardware-accelerated machine learning primitive operators. You can execute DirectML operations in isolation or as a graph (see the section [Layer-by-layer and graph-based workflows in DirectML](#layer-by-layer-and-graph-based-workflows-in-directml)).

Operators and graphs apply hardware-specific and architecture-specific optimizations. At the same time, you as the developer see a single, vendor-agnostic interface for executing those operators.

The library of operators in DirectML supplies all of the usual operations that you'd expect to be able to use in a machine learning workload.

- Activation operators, such as **linear**, **ReLU**, **sigmoid**, **tanh**, and more.
- Element-wise operators, such as **add**, **exp**, **log**, **max**, **min**, **sub**, and more.
- Convolution operators, such as 2D and 3D **convolution**, and more.
- Reduction operators, such as **argmin**, **average**, **l2**, **sum**, and more.
- Pooling operators, such as **average**, **lp**, and **max**.
- Neural network (NN) operators, such as **gemm**, **gru**, **lstm**, and **rnn**.
- And many more.

For maximal performance, and so that you don't pay for what you don't use, DirectML puts the control into your hands as a developer over how your machine learning workload is executed on the hardware. Figuring out which operators to execute, and when, is your responsibility as the developer. Tasks that are left to your discretion include: transcribing the model; simplifying and optimizing your layers; loading weights; resource allocation, binding, memory management (just as with Direct3D 12); and execution of the graph.

You retain high-level knowledge of your graphs (you can hard-code your model directly, or you can write your own model loader). You might design an upscaling model, for example, using several layers each of **upsample**, **convolution**, **normalization**, and **activation** operators. With that familiarity, careful scheduling, and barrier management, you can extract the most parallelism and performance from the hardware. If you're developing a game, then your careful resource management and control over scheduling enables you to interleave machine learning workloads and traditional rendering work in order to saturate the GPU.

## What's the high-level DirectML workflow?

Here's the high-level recipe for how we expect DirectML to be used. Within the two main phases of initialization and execution, you record work into command lists and then you execute them on a queue.

### Initialization

1. Create your Direct3D 12 resources&mdash;the Direct3D 12 device, command queue, command list, and resources such as descriptor heaps.
2. Since you're doing machine learning inferencing as well as your rendering workload, create DirectML resources&mdash;the DirectML device and operator instances. If you have a machine learning model where you need to perform a particular type of convolution with a particular size of filter tensor with a particular data type, then those are all parameters into DirectML's **convolution** operator.
3. DirectML records work into Direct3D 12 command lists. So, once initialization is done, you record the binding and initialization of (for example) your convolution operator into your command list. Then, close and execute your command list on your queue as usual.

### Execution

1. Upload your weight tensors into resources. A tensor in DirectML is represented using a regular Direct3D 12 resource. For example, if you want to upload your weight data to the GPU, then you do that the same way you would with any other Direct3D 12 resource (use an upload heap, or the copy queue).
2. Next, you need to bind those Direct3D 12 resources as your input and output tensors. Record into your command list the binding and the execution of your operators.
3. Close and execute your command list.

Just as with Direct3D 12, resource lifetime and synchronization are your responsibility. For example, don't release your DirectML objects at least until they've completed execution on the GPU.

## Layer-by-layer and graph-based workflows in DirectML

DirectML supports both layer-by-layer and graph-based approaches to model execution. When executing layer-by-layer, you're responsible for creating and initializing each DirectML operator, and individually recording them for execution on a command list. In contrast, when executing a graph you instead build a set of nodes and edges&mdash;where each node represents a DirectML operator, and edges represent tensor data flowing between nodes. The entire graph is then submitted for initialization or execution all at once, and DirectML handles the scheduling and recording of the individual operators on your behalf.

Both patterns are useful in different situations. A layer-by-layer approach gives you maximal control over the ordering and scheduling of compute work. For example, this level of control allows you to interleave Direct3D 12 rendering workloads with your DirectML compute dispatches. This can be useful for taking advantage of asynchronous compute or shader units on your GPU that would otherwise be idle. Manually executing layer by layer also gives explicit developer control over tensor layouts and memory usage.

However, machine learning models are often expressed in terms of *graphs* of layers. As an alternative to the layer-by-layer approach, DirectML allows you to express your model as a directed acyclic graph of nodes (DirectML operators) and edges between them (tensor descriptions). After building up a description of the graph, you can compile and submit it all at once to DirectML for initialization and execution. In this approach, DirectML decides on a traversal order, and handles each individual operator and the flow of data between them on your behalf. This is often a simpler and more natural way to express a machine learning model, and allow achitecture-specific optimizations to be applied automatically. Additionally, the [DirectMLX](dml-directmlx.md) helper library provides a clean and convenient syntax for building up complex graphs of DirectML operators.

Whichever approach you prefer, you'll always have access to the same extensive suite of DirectML operators. This means that you never have to sacrifice functionality whether you prefer the fine-grained control of the layer-by-layer approach, or the convenience of the graph approach.

## In this section

| Topic | Description |
|-|-|
| [QuickStart Guide](dml-get-started.md) | Get started using DirectML. |
| [Developer Tools](dml-tools.md) | Tools to profile, optimize, and debug DirectML. |
| Programming Guide | Topics on data binding, barriers, slides, fusions, error handling, device removal, and helper functions.
| Troubleshooting | Handling error conditions and using the debug layer. |
| [DirectML version history](dml-version-history.md) | DirectML is a system component of Windows 10, and is also available as a standalone redistributable package. |
| [DirectML feature level history](dml-feature-level-history.md) | A manifest of the types introduced in each feature level. |
| [DirectML sample applications](dml-min-app.md) | Links to DirectML sample applications, including a sample of a minimal DirectML application. |
| [GPU accelerated ML training](gpu-accelerated-training.md) | Covers what's currently supported by the GPU accelerated machine learning (ML) training for the [Windows Subsystem for Linux](/windows/wsl/about) (WSL) and native Windows. |
| [DirectML API reference](directml-reference.md) | This section covers Direct Machine Learning (DirectML) APIs declared in `DirectML.h`. |

## See also

* [DirectML GitHub repo](https://github.com/microsoft/DirectML)
* [Windows AI](../index.yml)
* [Direct3D 12 programming guide](/windows/win32/direct3d12/directx-12-programming-guide)
