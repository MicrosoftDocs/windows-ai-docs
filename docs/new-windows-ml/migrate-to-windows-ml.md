---
title: Migrate from standalone ONNX Runtime to Windows ML's ONNX Runtime
description: Learn how to migrate from using the standalone ONNX Runtime to using the ONNX Runtime included in Windows Machine Learning (ML) for hardware-optimized inference.
ms.date: 08/14/2025
ms.topic: how-to
---

# Migrate from standalone ONNX Runtime to Windows ML's ONNX Runtime

This guide explains how to migrate from using the [standalone ONNX Runtime](https://onnxruntime.ai/docs/) (the Microsoft-maintained, cross-platform runtime available via NuGet or GitHub) to the ONNX Runtime included in Windows ML.

## Why switch to Windows ML?

- **Smaller app download / install size** - Your app doesn't need to distribute large EPs and the ONNX Runtime
  - **EPs are dynamically downloaded via Windows ML**, so that you don't have to bundle them with your app
  - **The ONNX Runtime in Windows ML is a shared system-wide copy**, so your app doesn't have to bundle it with your app
- **Dynamically uses latest EPs** - Automatically downloads and manages the latest compatible hardware-specific execution providers, without requiring your app to update
- **Dynamically uses latest ONNX Runtime** - Automatically updates the ONNX Runtime without requiring your app to update. See the [ONNX versions](./onnx-versions.md) docs for more info

## System requirements for Windows ML

- **OS**: Windows 11 version 24H2 (build 26100) or later
- **Architecture**: x64 or ARM64
- **Hardware**: Any PC configuration (CPUs, integrated/discrete GPUs, NPUs)

## Step 1: Check ONNX version compatibility

See the [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md) docs to ensure Windows ML has the version of the ONNX Runtime your app requires. Make any updates to your models or code as necessary.

## Step 2: Check supported EPs

See the [supported execution providers in Windows ML](./supported-execution-providers.md) docs to ensure Windows ML supports the execution providers your app requires. Make any updates to your models or code as necessary.

## Step 3: Check Windows App SDK requirements

Windows ML is shipped via the Windows App SDK, and currently requires the **framework-dependent** deployment option. See the [use the Windows App SDK in an existing project](/windows/apps/windows-app-sdk/use-windows-app-sdk-in-existing-project) docs to ensure your app will be able to use Windows App SDK. Make any updates to your app as necessary.

## Step 4: Switch to Windows ML's ONNX Runtime

Remove the copy of the ONNX Runtime your app is currently using.

Then, follow Step 1 of the [get started with Windows ML](./get-started.md) docs to learn how to install the Windows App SDK (which contains Windows ML).

[!INCLUDE [C# tensors issue](./includes/csharp-tensors-issue.md)]

After installing Windows ML, C# and Python devs should be able to compile their app. The ONNX APIs in Windows ML are identical to the ONNX APIs in standalone ONNX Runtime. See [use ONNX APIs in Windows ML](./use-onnx-apis.md) for more info.

For C++ developers, there are two choices...

* Update your usage of the ONNX Runtime headers to use the Windows ML ONNX Runtime Headers, which are included in a `winml/` directory.
* OR, set the **WinMLEnableDefaultOrtHeaderIncludePath** property to true, so that the ONNX Runtime header paths will be the same as the standalone ONNX Runtime you were using before.

See [Use ONNX APIs](./use-onnx-apis.md) for more info on both options.

## Step 5: Initialize EPs via Windows ML

See the [initialize execution providers with Windows ML](./initialize-execution-providers.md) docs to learn how to dynamically initialize (download and register) EPs using Windows ML.

## Step 6: Run your app!

Your app should now be locally working with Windows ML!