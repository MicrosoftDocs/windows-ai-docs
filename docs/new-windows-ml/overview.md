---
title: What is Windows ML
description: Learn about how Windows Machine Learning (ML) helps your Windows apps run AI models locally.
ms.topic: article
ms.date: 07/07/2025
---

# What is Windows ML

Windows Machine Learning (ML) helps C#, C++, and Python Windows app developers run ONNX models locally across the entire variety of Windows PC hardware, including CPUs, GPUs, and NPUs.

:::image type="content" source="../images/winml-diagram.png" alt-text="A diagram illustrating an ONNX model going through Windows ML to then reach NPUs, GPUs, and CPUs.":::

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

If you're not already familiar with the ONNX Runtime, we suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/). In short, Windows ML provides a shared Windows-wide copy of the ONNX Runtime, plus the ability to dynamically download exection providers (EPs).

There are two major benefits of using Windows ML over using the ONNX Runtime directly. Windows ML provides...

* A shared system-wide copy of the latest ONNX Runtime, meaning your app doesn't have to carry the ONNX Runtime, reducing your app's download and install size.
* A shared system-wide ability to dynamically download, register, and update ONNX execution providers (EPs), meaning your app doesn't have to distribute or store its own copies of EPs for specific GPUs and NPUs, drastically reducing your app's download and install size while still letting you leverage the best local hardware on each PC.

## Supported Windows versions

Windows ML works on all Windows 11 PCs running version 24H2 (build 26100) or greater.

## Supported hardware

Windows ML works on all x64 and ARM64 PC hardware, even PCs that don't have NPUs or GPUs, which means you can reach hundreds of millions of Windows devices in the market. That might mean keeping the workloads light; but there are iGPUs that are powerful enough to handle heavy workloads.

## Converting models to ONNX

You can convert models from other formats to ONNX so that you can use them with Windows ML. See the Visual Studio Code AI Toolkit's docs about how to [convert models to the ONNX format](https://code.visualstudio.com/docs/intelligentapps/modelconversion) to learn more.

## Detailed overview

Windows ML in `Microsoft.Windows.AI.MachineLearning` serves as the AI inferencing *nucleus* of the Windows AI Foundry. So whether you're using the [Windows AI APIs](../apis/index.md) to access the models that are built into Windows, or you're using the growing list of ready-to-use Foundry models with Foundry Local (see [Get started with Foundry Local](../foundry-local/get-started.md)), you'll be running your AI workloads on Windows ML likely without even knowing it.

And if you're bringing your own models, or if you need a high degree of fine-grained control over how model inferencing takes place, then you can use Windows ML directly by calling its APIs. See [Windows ML (Microsoft.Windows.AI.MachineLearning) APIs](./api-reference.md).

### Based on the ONNX Runtime

Windows ML includes a system-wide shared copy of the [ONNX Runtime](https://github.com/microsoft/onnxruntime).

The ONNX Runtime talks to silicon via execution providers (EPs), which serve as a translation layer between the runtime and hardware drivers. We've taken the execution provider work we did with Windows Click to Do and NPUs, combined that with new execution providers for GPUs, and wrapped it all into a single Windows ML framework that now fully delivers on the promise of enabling AI workloads that can target any hardware across CPU, GPU, and NPU. Each type of processor is a first-class citizen that's fully supported with the latest drivers and ONNX Runtime execution providers from the four major AI silicon vendors (AMD, Intel, NVIDIA, and Qualcomm). Those processor types are on equal footing&mdash;you need only to write to Windows ML with ONNX QDQ models in order to scale your AI workloads confidently across all types of hardware.

### Packaging and deployment

Windows ML is distributed as part of the Windows App SDK. After you add a reference to the Windows App SDK in your project, and install your app on a customer's PC:
1. The Windows App SDK bootstrapper ensures the Windows ML runtime is properly initialized in your app.
2. Then Windows ML detects the hardware for the specific machine your app is installed on, and downloads the appropriate execution providers needed for that PC.

So you don't need to carry your own execution providers in your app package. In fact, you don't need to worry about execution providers at all, or about shipping custom builds of AI runtimes that are specifically designed for AMD or Intel or NVIDIA or Qualcomm or any other specific family of hardware. You simply call Windows ML APIs, then feed in a properly formatted model, and we take care of the rest&mdash;automatically provisioning everything needed on the target hardware, and keeping everything up to date.

The result is that it greatly simplifies the dependencies that you have to manage and worry about. And it's made possible by the level of interaction that we've benefited from with hardware partners such as AMD, Intel, NVIDIA, and Qualcomm. Those partners will continue to provide execution providers for Windows ML, and submit them to Microsoft when they have updates or new silicon they're introducing to the market.

Microsoft will certify any new execution providers (EPs) to ensure that there are no regressions to inferencing accuracy. And then we'll take on the responsibility of deploying those EPs to target machines on the hardware vendors' behalf, and facilitate the Windows ML ecosystem as a whole staying current and up to date.

It's a different approach from the approach taken by technologies such as DirectML and DirectX; where Microsoft abstracts APIs over hardware ecosystem changes. Instead, with Windows ML, we're changing the landscape to empower hardware vendors to rapidly and directly introduce innovative silicon, with immediate day-1 execution provider support for that hardware as it arrives in market.

## Performance

Performance is more than pure wall-clock speed. Yes, a lot of AI workloads are computationally expensive. But as AI becomes ubiquitous in app experiences, there's a need for a runtime that can optimize inferencing in a way that preserves battery life while maintaining a high degree of accuracy. So that the AI produces good, accurate results.

AI workloads typically fall into one of two buckets:
1. **Ambient AI**. The AI is happening silently in the background as users interact with your app.
2. **Explicit AI**. Users know that they've launched an AI task, which is commonly some sort of generative AI (genAI) scenario.

Ambient AI workloads can be offloaded to a dedicated NPU processor with 40+ TOPS of processing power, and drawing power usually in the ones of watts. In that way, Windows ML is perfect for ambient AI workloads. In most cases, the users of your apps will feel the magic of AI without having to wait, and without having to worry about their PC's battery life.

A lot of computationally heavy AI tasks can be best served by a dedicated GPU. The 2018 version of Windows ML relies on a DirectML EP to handle GPU workloads; and that increases the number of layers between your model and the silicon. Windows ML in `Microsoft.Windows.AI.MachineLearning` doesn't have the DirectML layer. Instead, it works directly with dedicated execution providers for the GPU, affording you to-the-metal performance that's on par with dedicated SDKs of the past such as TensorRT for RTX, AI Engine Direct, and Intel's Extension for PyTorch. We've engineered Windows ML to have best-in-class GPU performance, while retaining the write-once-run-anywhere benefits that the past DirectML-based solution offered.

In both of the above two cases, all aspects of performance matter. Pure wall-clock speed, battery life, and accuracy. So that the the user gets actually-good results.

All of this opens up to you a range of AI-powered experiences and scenarios. You can run ambient AI workloads and agents on dedicated NPUs; or run workloads on integrated GPUs to keep the discrete GPU free if needed. And if you want raw power, then you can target today's modern discrete GPUs (dGPUs) to run heavier workloads at the fastest possible speeds.

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

## Providing feedback about Windows ML

We would love to hear your feedback about using Windows ML! If you run into any issues or have suggestions, please search the [Windows App SDK GitHub](https://github.com/microsoft/WindowsAppSDK/issues) to see if it has already been reported, and if not, create a new issue.
