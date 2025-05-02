---
title: Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)
description: With Windows Machine Learning (ML) types in the Microsoft.Windows.AI.MachineLearning namespace, you can build hardware-abstracted AI inferencing capabilities into your Windows apps.
ms.date: 04/29/2025
ms.topic: article
---

# Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)

For API reference content, see [Windows ML APIs in Microsoft.Windows.AI.MachineLearning](./api-reference.md).

With Windows Machine Learning (ML) types in the **Microsoft.Windows.AI.MachineLearning** namespace, you can build hardware-abstracted AI inferencing capabilities into your Windows apps. With Windows ML, your app can download execution providers (EPs) that are available from the Microsoft Store, or from anywhere that MSIX packages are provided. You can then load and use those EPs.

The APIs in **Microsoft.Windows.AI.MachineLearning** make it easier for you as a developer to build apps that use machine learning (ML) without your having to manually manage the underlying execution provider (EP) packages. The APIs handle downloading, updating, and initializing EPs; which you can then continue to use with **Microsoft.Windows.AI.MachineLearning**, and/or with the [ONNX Runtime](https://onnxruntime.ai/).

## Using execution providers with Windows ML

The Windows ML runtime provides a flexible way to access machine learning (ML) execution providers (EPs), which can optimize ML model inference on different hardware configurations. Those EPs are distributed as separate packages that can be updated independently from the operating system.

## The Microsoft.Windows.AI.MachineLearning NuGet package

The APIs in **Microsoft.Windows.AI.MachineLearning** are distributed as a NuGet package, making it easy to integrate into your projects. This topic covers in detail the following high-level steps:

1. In Visual Studio, add the *Microsoft.Windows.AI.MachineLearning* NuGet package to your project. For the latest release info, see the [NuGet package README](../packaging/WcrNuGet/README.md).
2. Initialize the **Infrastructure** class.
3. Download and load execution providers (EPs).
4. Use the EPs with your ML framework.

## NuGet package components

The *Microsoft.Windows.AI.MachineLearning* NuGet package includes the following:

* C++/WinRT projections for native C++ apps.
* WinMD metadata and projections for C# apps.
* Required dependencies and redistributable components.

## Project configuration

For detailed integration instructions, including platform-specific considerations and minimum system requirements, see the [NuGet Package README](../packaging/WcrNuGet/README.md).

### [C# Visual Studio project](#tab/csharp-projects)

```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

### [C++/WinRT Visual Studio project](#tab/cppwinrt-projects)

```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

---

## What is an execution provider?

An execution provider (EP) is a component that implements hardware-specific optimizations for machine learning (ML) operations. An EP can implement one or more hardware abstractions. For example:

* CPU execution providers optimize for general-purpose processors.
* GPU execution providers optimize for graphics processors.
* NPU execution providers optimize for neural processing units.
* Vendor-specific providers such as OpenVINO, VitisAI, QNN, and others.

The Windows ML runtime handles the complexity of managing those execution providers by providing APIs to do the following:

1. Download the appropriate EPs for the current hardware.
2. Load EPs dynamically at runtime.
3. Configure EP behavior.

## Versioning of execution providers

The Windows ML runtime supports these two versioning modes for execution providers (EPs):

* **Floating versions** (default). Use the latest compatible version of EPs matching the same major version (x.*.*). This allows apps to benefit from performance improvements and support for new operators without requiring changes to your app.
* **Fixed versions**. An opt-in mode that uses specific versions of EPs that were tested with the Windows ML runtime that's being used (x.y.*). This provides deterministic behavior for apps that require consistency.

EP packages follow a semantic versioning (SemVer) approach:

* Major and minor version components are encoded into the *Package Name*.
* *Package Version* is used for patch versions.

That packaging approach enables the two versioning modes while maintaining compatibility with Microsoft Store and MSIX deployment mechanisms.

## ABI stability

The primary interface between the Windows ML runtime and execution providers (EPs) is through the ONNX Runtime ABI. Any version of the Windows ML runtime carries a specific version of the ONNX Runtime implementing a particular ABI version. EP packages implementing that ABI version and later (within the same major version) will function properly.

The Windows ML runtime is designed to support at least the past three minor ABI versions at any given time; ensuring backwards compatibility with existing EP packages.

## Basic workflow

The typical workflow for using execution providers (EPs) involves these steps:

1. Initialize the **Microsoft.Windows.AI.MachineLearning.Infrastructure** class.
2. Call **DownloadPackagesAsync** to ensure that the latest execution providers are available.
3. Call **LoadExecutionProvidersAsync** to retrieve the list of EPs.
4. Pass those EPs to your ML framework (Windows ML and/or ONNX Runtime).

## 📌 Video Placeholder: Insert video here

## See also

* [Windows ML APIs in Microsoft.Windows.AI.MachineLearning](./api-reference.md)
