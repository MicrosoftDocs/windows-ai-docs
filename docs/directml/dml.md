---
title: Direct Machine Learning (DirectML)
description: Direct Machine Learning (DirectML) is a low-level API for machine learning. It has a familiar (native C++, nano-COM) programming interface and workflow in the style of DirectX 12.
ms.custom: Windows 10 May 2019 Update
ms.localizationpriority: high
ms.topic: article
ms.date: 04/19/2019
author: stevewhims
ms.author: stwhi
---

# Direct Machine Learning (DirectML)

Direct Machine Learning (DirectML) is a low-level API for machine learning. It has a familiar (native C++, nano-COM) programming interface and workflow in the style of DirectX 12. You can integrate machine learning inferencing workloads into your game, engine, middleware, backend, or other application. DirectML is supported by all DirectX 12-compatible hardware.

DirectML is introduced in Windows 10, version 1903, and in the corresponding version of the Windows SDK.

## In this section

| Topic | Description |
|-|-|
| [Introduction to DirectML](dml-intro.md) | Direct Machine Learning (DirectML) is a low-level API for machine learning (ML). |
| [DirectML version history](dml-version-history.md) | DirectML is a system component of Windows 10, and is also available as a standalone redistributable package. |
| [DirectML feature level history](dml-feature-level-history.md) | A manifest of the types introduced in each feature level. |
| [Binding in DirectML](dml-binding.md) | In DirectML, *binding* refers to the attachment of resources to the pipeline for the GPU to use during the initialization and execution of your machine learning operators. These resources can be input and output tensors, for example, as well as any temporary or persistent resources that the operator needs. |
| [UAV barriers and resource state barriers in DirectML](dml-barriers.md) | Describes the correctness benefits of barriers, and how you can work with them in DirectML. |
| [Resource lifetime and synchronization](dml-resource-lifetime.md) | In order to avoid undefined behavior, your DirectML application must correctly manage object lifetimes and synchronization between the CPU and the GPU. |
| [Using strides to express padding and memory layout](dml-strides.md) | DirectML tensors are described by properties known as the *sizes* and the *strides* of the tensor. |
| [Using fused operators to improve performance](dml-fused-activations.md) | Some DirectML operators support a concept known as *fusion*. Operator fusion is a way to improve performance by merging one operator (typically, an activation function) into a different operator so that they are executed together without requiring a roundtrip to memory. |
| [Using the DirectML debug layer](dml-debug-layer.md) | The DirectML debug layer is an optional development-time component that helps you in debugging your DirectML code. |
| [Handling errors and device-removal](dml-errors.md) | This topic discusses how to debug DirectML device-removal, and other error conditions. |
| [DirectMLX](dml-directmlx.md) | DirectMLX is a C++ header-only helper library for DirectML, intended to make it easier to compose individual operators into graphs. |
| [DirectML helper functions](dml-helper-functions.md) | Code listings of essential DirectML helper functions. |
| [DirectML sample applications](dml-min-app.md) | Links to DirectML sample applications, including a sample of a minimal DirectML application. |
| [GPU accelerated ML training](gpu-accelerated-training.md) | Covers what's currently supported by the GPU accelerated machine learning (ML) training for the [Windows Subsystem for Linux](/windows/wsl/about) (WSL) and native Windows. |
| [DirectML API reference](directml-reference.md) | This section covers Direct Machine Learning (DirectML) APIs declared in `DirectML.h`. |

## Related topics

* [Windows AI](../index.yml)
* [Direct3D 12 programming guide](/windows/win32/direct3d12/directx-12-programming-guide)
* [DirectML on GitHub](https://github.com/microsoft/DirectML)

