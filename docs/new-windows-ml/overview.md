---
title: What is Windows ML
description: Learn about how Windows Machine Learning (ML) helps your Windows apps run AI models locally.
ms.topic: article
ms.date: 07/07/2025
---

# What is Windows ML

Windows Machine Learning (ML) enables C#, C++, and Python developers to run ONNX AI models locally on Windows PCs, with automatic execution provider management for different hardware (CPUs, GPUs, NPUs).

:::image type="content" source="../images/winml-diagram.png" alt-text="A diagram illustrating an ONNX model going through Windows ML to then reach NPUs, GPUs, and CPUs.":::

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps using these APIs should not be published to the Microsoft Store.

If you're not already familiar with the ONNX Runtime, we suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/). In short, Windows ML provides a shared Windows-wide copy of the ONNX Runtime, plus the ability to dynamically download execution providers (EPs).

## Key benefits

- **Dynamically get latest EPs** - Automatically downloads and manages the latest hardware-specific execution providers
- **Shared [ONNX Runtime](https://onnxruntime.ai/docs/)** - Uses system-wide runtime instead of bundling your own, reducing app size
- **Smaller downloads/installs** - No need to carry large EPs and the ONNX Runtime in your app
- **Broad hardware support** - Runs on all Windows 11 PCs (x64 and ARM64) with any hardware configuration

## System requirements

- **OS**: Windows 11 version 24H2 (build 26100) or later
- **Architecture**: x64 or ARM64
- **Hardware**: Any PC configuration (CPUs, integrated/discrete GPUs, NPUs)

## How it works

Windows ML is built on the [ONNX Runtime](https://onnxruntime.ai/) and uses **execution providers** (EPs) to optimize inference for different hardware:

- **CPU EPs** - General-purpose processors
- **GPU EPs** - Graphics processors (integrated and discrete)  
- **NPU EPs** - Neural processing units
- **Vendor-specific EPs** - AMD, Intel, NVIDIA, Qualcomm optimizations

### Automatic deployment

1. **App installation** - Windows App SDK bootstrapper initializes Windows ML
2. **Hardware detection** - Runtime identifies available processors  
3. **EP download** - Automatically downloads optimal execution providers
4. **Ready to run** - Your app can immediately use AI models

This eliminates the need to:
- Bundle execution providers for specific hardware vendors
- Create separate app builds for different execution providers
- Handle execution provider updates manually

> [!NOTE]
> You're still responsible for optimizing your models for different hardware. Windows ML handles execution provider distribution, not model optimization.

## Performance optimization

The latest version of Windows ML works directly with dedicated execution providers for GPUs and NPUs, affording you to-the-metal performance that's on par with dedicated SDKs of the past such as TensorRT for RTX, AI Engine Direct, and Intel's Extension for PyTorch. We've engineered Windows ML to have best-in-class GPU and NPU performance, while retaining the write-once-run-anywhere benefits that the previous DirectML-based solution offered.

## What is an execution provider?

An execution provider (EP) is a component that implements hardware-specific optimizations for machine learning (ML) operations. An EP can implement one or more hardware abstractions. For example:

* CPU execution providers optimize for general-purpose processors.
* GPU execution providers optimize for graphics processors.
* NPU execution providers optimize for neural processing units.
* Other vendor-specific providers.

The Windows ML runtime handles the complexity of managing those execution providers by providing APIs to do the following:

1. Download the appropriate EPs for the current hardware.
2. Register EPs dynamically at runtime.
3. Configure EP behavior.

## Using execution providers with Windows ML

The Windows ML runtime provides a flexible way to access machine learning (ML) execution providers (EPs), which can optimize ML model inference on different hardware configurations. Those EPs are distributed as separate packages that can be updated independently from the operating system.

## Converting models to ONNX

You can convert models from other formats to ONNX so that you can use them with Windows ML. See the Visual Studio Code AI Toolkit's docs about how to [convert models to the ONNX format](https://code.visualstudio.com/docs/intelligentapps/modelconversion) to learn more.

## Integration with Windows AI ecosystem

Windows ML serves as the foundation for the broader Windows AI platform:

- **[Windows AI APIs](../apis/index.md)** - Built-in models for common tasks
- **[Foundry Local](../foundry-local/get-started.md)** - Ready-to-use AI models  
- **Custom models** - Direct Windows ML API access for advanced scenarios

## Next steps

- **Get started**: [Windows ML API reference](./api-reference.md)
- **Learn more**: [ONNX Runtime documentation](https://onnxruntime.ai/docs/)
- **Convert models**: [VS Code AI Toolkit model conversion](https://code.visualstudio.com/docs/intelligentapps/modelconversion)

## Feedback

Found an issue or have suggestions? Search or create issues on the [Windows App SDK GitHub](https://github.com/microsoft/WindowsAppSDK/issues).
