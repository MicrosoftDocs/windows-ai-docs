---
title: Get started with Windows ML
description: Install Windows ML and run your first ONNX model locally on Windows.
ms.date: 04/28/2026
ms.topic: how-to
---

# Get started with Windows ML

This topic walks you through the minimal path to running an ONNX model with Windows ML on CPU, then points you to hardware acceleration when you're ready.

To learn more about Windows ML, see [What is Windows ML](./overview.md).

## Prerequisites

* Version of Windows that [Windows App SDK supports](/windows/apps/windows-app-sdk/support)
* Architecture: x64 or ARM64
* Language-specific prerequisites seen below

### [C#](#tab/csharp)

* .NET 8 or greater to use all Windows ML APIs
  * With .NET 6, you can install execution providers using the `Microsoft.Windows.AI.MachineLearning` APIs, but you cannot use the `Microsoft.ML.OnnxRuntime` APIs.
* Targeting a Windows 10-specific TFM like `net8.0-windows10.0.17763.0` or greater

### [C++/WinRT](#tab/cppwinrt)

C++20 or later.

### [C/C++](#tab/c)

* Visual Studio 2022 with C++ workload (includes the C compiler)
* CMake 3.21 or later

### [Python](#tab/python)

Python versions 3.10 to 3.13, on x64 and ARM64 devices.

---

## Step 1: Find a model

Before writing any code, you need an ONNX model. See [Find or train models](./models.md) for guidance on obtaining ONNX models.

## Step 2: Install Windows ML

See [Install and deploy Windows ML](./distributing-your-app.md) for full instructions across all supported languages and deployment modes (framework-dependent and self-contained).

## Step 3: Add namespaces / headers

After you've installed Windows ML in your project, see [Use ONNX APIs](./use-onnx-apis.md) for guidance about which namespaces / headers to use.

## Step 4: Run an ONNX model

With Windows ML installed, you can run ONNX models on the CPU with no additional setup. See [Run ONNX models](./run-onnx-models.md) for guidance.

At this point your app has a working inference path on CPU.

## Step 5: Optionally accelerate on NPU or GPU

Want faster inference on NPU, GPU, or even CPU? See [Accelerate AI models](./accelerate-ai-models.md) to add hardware-tuned execution providers for your target hardware.

## See also

* **[Accelerate AI models](./accelerate-ai-models.md)** - Add NPU, GPU, or CPU execution providers
* **[Run ONNX models](./run-onnx-models.md)** - Info about inferencing ONNX models
* **[Install and deploy Windows ML](./distributing-your-app.md)** - Options for deploying an app using Windows ML
* **[Tutorial](./tutorial.md)** - Full end-to-end tutorial using Windows ML with the ResNet-50 model
* **[Code samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML)** - Our code samples using Windows ML