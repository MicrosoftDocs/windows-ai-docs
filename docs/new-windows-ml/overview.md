---
title: What is Windows ML
description: Learn about how Windows Machine Learning (ML) helps your Windows apps run AI models locally.
ms.topic: article
ms.date: 05/12/2025
---

# What is Windows ML

Windows Machine Learning (ML) helps C#, C++, and Python Windows app developers run ONNX models locally across the entire variety of Windows PC hardware, including CPUs, GPUs, and NPUs. Windows ML abstracts the hardware and execution providers, so you can focus on writing your code. Plus, Windows ML automatically updates to support the latest NPUs, GPUs, and CPUs as they are released.

> [!NOTE]
> These Windows ML APIs (in the `Microsoft.Windows.AI.MachineLearning` namespace shipped via NuGet) supersede the version of Windows ML that shipped in 2018 in Windows in the `Windows.AI.MachineLearning` namespace.

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

## Supported Windows versions

Windows ML works on all Windows 11 PCs running version 24H2 (build 26100) or greater.

## Supported hardware

Windows ML works on all x64 and ARM64 PC hardware, even PCs that don't have NPUs or GPUs, which means you can reach hundreds of millions of Windows devices in the market. That might mean keeping the workloads light; but there are iGPUs (the Strix Halo, for example) that are powerful enough to handle heavy workloads.

Not only does Windows ML support all types of processors, it automatically keeps itself up to date, and adapts to future generations of CPUs, GPUs, and NPUs as they release.

As new hardware releases to market, Windows ML updates itself to use a newer version of the execution provider. That provides day-1 support for that hardware as it emerges. So when your app uses Windows ML, it's future-proofed to always run on the latest-generation hardware in the Windows ecosystem.

A new certification program that we're introducing makes this possible. It's similar in spirit to driver certification and ensures that as execution providers are created and updated, the accuracy of AI inferencing on those providers is maintained.

## What challenges does Windows ML address?

### Hardware diversity

As an AI developer building on Windows, the first challenge that Windows ML addresses for you is that of hardware diversity. Yes, it's an advantage of the Windows ecosystem that users can choose whatever hardware best suits them. But without Windows ML, that would make it difficult for you, as a developer building AI experiences, to support all of that hardware diversity. Many of today's leading apps that run on Windows choose to release on just a single hardware vendor at a time. Just intel; just Qualcomm; just AMD; just discrete GPUs for now. And that greatly limits the number of devices that those apps are able to run on.

### Deploying dependencies

Next, there's the problem of deploying all of the dependencies you need. To ship AI experiences in your app, your app must ship and deploy three elements.

* The AI models that you want to run.
* A runtime that allows you to perform inferencing on those models.
* Vendor-specific tools and drivers that help your chosen runtime to talk to the silicon.

Your app needs those things, and it also needs them to be maintained and updated. When a new version of the runtime releases, or a critical bug is fixed in it, you'd need to update your app accordingly. Without Windows ML, as a developer of apps, you'd need to take ownership of all of those dependencies. They'd become a part of your app, and the burden of maintaining everything would fall to you.

### Leveraging local hardware

And then there's the issue of putting to work the local hardware that your app is running on. Should your AI workloads run on CPU, or GPU, or NPU?   If you're using different AI models, then which ones run best on which processors? This problem rapidly gets very complex. And without Windows ML it would be up to you to write and maintain the difficult logic that first detects what's available on the current device, and then tries to get the most performance out of it.

Windows ML in `Microsoft.Windows.AI.MachineLearning` solves all of these those problems.

* The runtime doesn't need to be inside your app.
* The execution provider (EP) is selected for your users automatically based on the hardware that's available to them. Developer overrides are available for selection.
* Windows ML manages your runtime dependencies; pushing the burden *out* of your app and onto Windows ML itself and the EPs.
* Windows ML helps balance the load on the client device, and chooses the appropriate hardware for the execution of the AI workload.

## Detailed overview

Windows ML in `Microsoft.Windows.AI.MachineLearning` serves as the AI inferencing *nucleus* of the Windows AI Foundry. So whether you're using the [Windows AI APIs](../apis/index.md) to access the models that are built into Windows, or you're using the growing list of ready-to-use Foundry models with Foundry Local (see [Get started with Foundry Local](../foundry-local/get-started.md)), you'll be running your AI workloads on Windows ML likely without even knowing it.

And if you're bringing your own models, or if you need a high degree of fine-grained control over how model inferencing takes place, then you can use Windows ML directly by calling its APIs. See [Windows ML (Microsoft.Windows.AI.MachineLearning) APIs](./api-reference.md).

### Based on the ONNX Runtime

Windows ML is built on a forked and specialized [version of the ONNX Runtime](https://github.com/microsoft/onnxruntime). Doing so enables some Windows-specific enhancements to performance. We also optimized it around standard ONNX QDQ models, which allows a focus on achieving the best inferencing performance on the local device without needing to enlarge the models unnecessarily.

The ONNX Runtime talks to silicon via execution providers (EPs), which serve as a translation layer between the runtime and hardware drivers. We've taken the execution provider work we did with Windows Recall and NPUs, combined that with new execution providers for GPUs, and wrapped it all into a single Windows ML framework that now fully delivers on the promise of enabling AI workloads that can target any hardware across CPU, GPU, and NPU. Each type of processor is a first-class citizen that's fully supported with the latest drivers and ONNX Runtime execution providers from the four major AI silicon vendors (AMD, Intel, NVIDIA, and Qualcomm). Those processor types are on equal footing&mdash;you need only to write to Windows ML with ONNX QDQ models in order to scale your AI workloads confidently across all types of hardware.

### Packaging and deployment

After you add a reference to Windows ML to your project, and install your app on a customer's PC:
1. We download the latest version of Windows ML itself, in order to ensure that the runtime is properly installed alongside your app.
2. Then we detect the hardware for the specific machine your app is installed on, and download the appropriate execution providers needed for that PC.

So you don't need to carry your own execution providers in your app package. In fact, you don't need to worry about execution providers at all, or about shipping custom builds of AI runtimes that are specifically designed for AMD or Intel or NVIDIA or Qualcomm or any other specific family of hardware. You simply call Windows ML APIs, then feed in a properly formatted model, and we take care of the rest&mdash;automatically provisioning everything needed on the target hardware, and keeping everything up to date.

The result is that it greatly simplifies the dependencies that you have to manage and worry about. And it's made possible by the level of interaction that we've benefited from with hardware partners such as AMD, Intel, NVIDIA, and Qualcomm. Those partners will continue to provide execution providers for Windows ML, and submit them to Microsoft when they have updates or new silicon they're introducing to the market.

Microsoft will certify any new execution providers (EPs) to ensure that there are no regressions to inferencing accuracy. And then we'll take on the responsibility of deploying those EPs to target machines on the hardware vendors' behalf, and facilitate the Windows ML ecosystem as a whole staying current and up to date.

It's a different approach from the approach taken by technologies such as DirectML and DirectX; where Microsoft abstracts APIs over hardware ecosystem changes. Instead, with Windows ML, we're changing the landscape to empower hardware vendors to rapidly and directly introduce innovative silicon, with immediate day-1 execution provider support for that hardware as it arrives in market.

## Performance

Performance is more than pure wall-clock speed. Yes, a lot of AI workloads are computationally expensive. But as AI becomes ubiquitous in app experiences, there's a need for a runtime that can optimize inferencing in a way that preserves battery life while maintaining a high degree of accuracy. So that the AI produces good, accurate results.

AI workloads typically fall into one of two buckets:
1. **Ambient AI**. The AI is happening silently in the background as users interact with your app.
2. **Explicit AI**. Users know that they've launched an AI task, which is commonly some sort of generative AI (genAI) scenario.

Because Windows ML supports NPUs as a first-class citizen, ambient AI workloads can be offloaded to a dedicated processor with 40+ TOPS of processing power, and drawing power usually in the ones of watts. In that way, Windows ML is perfect for ambient AI workloads. In most cases, the users of your apps will feel the magic of AI without having to wait, and without having to worry about their PC's battery life.

But not every machine has an NPU. And a lot of computationally heavy AI tasks can be best served by a dedicated GPU. The 2018 version of Windows ML relies on a DirectML EP to handle GPU workloads; and that increases the number of layers between your model and the silicon. Windows ML in `Microsoft.Windows.AI.MachineLearning` doesn't have the DirectML layer. Instead, it works directly with dedicated execution providers for the GPU, affording you to-the-metal performance that's on par with dedicated SDKs of the past such as TensorRT, AI Engine Direct, and Intel's Extension for PyTorch. We've engineered Windows ML to have best-in-class GPU performance, while retaining the write-once-run-anywhere benefits that the past DirectML-based solution offered.

In both of the above two cases, all aspects of performance matter. Pure wall-clock speed, battery life, and accuracy. So that the the user gets actually-good results.

All of this opens up to you a range of AI-powered experiences and scenarios. You can run ambient AI workloads and agents on dedicated NPUs; or run workloads on integrated GPUs to keep the discrete GPU free if needed. And if you want raw power, then you can target today's modern discrete GPUs (dGPUs) to run heavier workloads at the fastest possible speeds.

## What is an execution provider?

An execution provider (EP) is a component that implements hardware-specific optimizations for machine learning (ML) operations. An EP can implement one or more hardware abstractions. For example:

* CPU execution providers optimize for general-purpose processors.
* GPU execution providers optimize for graphics processors.
* NPU execution providers optimize for neural processing units.
* Vendor-specific providers such as OpenVINO, VitisAI, QNN, and others.

The Windows ML runtime handles the complexity of managing those execution providers by providing APIs to do the following:

1. Download the appropriate EPs for the current hardware.
2. Register EPs dynamically at runtime.
3. Configure EP behavior.

## Using execution providers with Windows ML

The Windows ML runtime provides a flexible way to access machine learning (ML) execution providers (EPs), which can optimize ML model inference on different hardware configurations. Those EPs are distributed as separate packages that can be updated independently from the operating system.

## Samples

You needn't take our word for it. Download and run the samples linked below to begin trying out Windows ML for yourself today.
