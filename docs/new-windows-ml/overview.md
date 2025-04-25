---
title: Introducing Windows ML (Microsoft.Windows.AI.MachineLearning)
description: TBD
ms.topic: article
ms.date: 04/25/2025
---

# Introducing Windows ML (Microsoft.Windows.AI.MachineLearning)

Windows Machine Learning (ML) provides types with which you can build hardware-abstracted AI inferencing capabilities into your Windows apps. The version of Windows Machine Learning (ML) that shipped in 2018 in the `Windows.AI.MachineLearning` namespace is superseded by a version of Windows ML that's in the `Microsoft.Windows.AI.MachineLearning` namespace. And this change is the result of Microsoft's growth in shipping AI experiences, as well as external and internal feedback.

## What challenges does Windows ML address?

### Hardware diversity

As an AI developer building on Windows, the first challenge that Windows ML addresses for you is that of hardware diversity. Yes, it's an advantage of the Windows ecosystem that users can choose whatever hardware best suits them. But without Windows ML, that would make it difficult for you, as a developer building AI experiences, to support all of that hardware diversity. Many of today's leading apps that run on Windows choose to release on just a single hardware vendor at a time. Just intel; just Qualcomm; just discrete GPUs for now. And that greatly limits the number of devices that those apps are able to run on.

### Deploying dependencies

Next, there's the problem of deploying all of the dependencies you need. To ship AI experiences in your app, your app must ship and deploy three elements.

* The AI models that you want to run.
* A runtime that allows you to inference those models.
* Vendor-specific tools and drivers that help your chosen runtime to talk to the silicon.

Your app needs those things, and it also needs them to be maintained and updated. When a new version of the runtime releases, or a critical bug is fixed in it, you'd need to update your app accordingly. Without Windows ML, as a developer of apps, you'd need to take ownership of all of those dependencies. They'd become a part of your app, and the burden of maintaining everything would fall to you.

### Leveraging local hardware

And then there's the issue of putting to work the local hardware that your app is running on. Should your AI workloads run on CPU, or GPU, or NPU?  What if the processor is overloaded; how will you know, and shift the workload to silicon that has more capacity? If you're using different AI models, then which ones run best on which processors? This problem rapidly gets very complex. And  quickly, and Without Windows ML it would be up to you to write and maintain the difficult logic that first detects what's available on the current device, and then tries to get the most performance out of it.

Windows ML in the `Microsoft.Windows.AI.MachineLearning` namespace solves all of these these problems. The runtime doesn't need to be inside your app. The execution provider (EP) is selected for your users automatically based on the hardware that's available to them. Windows ML manages your runtime dependencies, pushing the burden *out* of your app and onto Windows ML itself and the EPs. Windows ML also helps balance the load on the client device, and choose the appropriate hardware for the execution of the AI workload.

## Detailed overview

This new version of WinML serves as the AI inferencing 'nucleus' of the Windows AI Foundry. So, if you're using the Windows AI APIs to access the built-into-Windows models we offer, or are using our growing list of ready-to-use Foundry models with Foundry Local, you'll be running your AI workloads on Windows ML without even knowing it

However, if you're bringing your own models, or just need a high degree of fine-grained control over how model inference happens, you can use Windows ML directly via its APIs.

This new Windows ML is built on a forked and specialized version of the ONNX runtime. This allows us to make some Windows-specific enhancements to performance. We are also optimizing it around standard ONNX QDQ models, which enables a focus on getting the best inference performance on the local device without needed to enlarge the models unnecessarily.

The ONNX runtime talks to silicon via Execution Providers (EPs) which serve as a translation layer between the runtime and hardware drivers. We've taken the execution provider work we did with Windows Recall and NPUs, combined that with new execution providers for GPUs, and wrapped that all into a single Windows ML framework that now fully delivers on the promise of enabling AI workloads that can target any hardware across CPU, GPU, and NPU: each type of processor is a first class citizen that is fully supported with the latest drivers and ONNX Runtime Execution Providers from the 4 major AI silicon vendors: Nvidia, AMD, Intel, and Qualcomm. These processor types are now on equal footing – just write to Windows ML with ONNX QDQ models, and scale your AI workloads confidently across all types of hardware.

Windows ML isn't limited to only Copilot+ PCs – it will work on any supported Windows PC, which means you'll be able to target the hundreds of millions of Windows devices already in the market today that have powerful GPUs, but aren't Copilot+ PCs due to the lack of an NPU. You can even use Windows ML on machines with only a CPU and iGPU, so long as you keep the workloads light enough for those processors to handle it.

Furthermore, not only does it work on all types of processors, but it will automatically keep itself up to date and adapt to future generations of CPUs, GPUs, and NPUs as they come out.

When new hardware is released to market, Windows ML will update itself to use a new version of the execution provider, to provide day 1 support for that hardware as it comes to market. This means that if your app uses WinML, it is future proofed to always run on the latest generation hardware in the Windows ecosystem.

This will be made possible via a new certification program we're introducing, similar in spirit to driver certification, to ensure that as execution providers are created and updated, the accuracy of AI inference on those providers is maintained.

Once you reference WinML in your app code and install the app to a target customer's PC, a couple things will happen: 1) first, we'll download the latest version of WinML itself to ensure the runtime is properly installed alongside your app. 2) then, we'll also detect the hardware for the specific machine your app is installing to and download the appropriate driver and execution providers needed for that PC.

This means you don't need to carry your own execution providers in your app package, and in fact you don't even need to worry about execution providers at all, or shipping custom builds of AI runtimes that are specifically designed for Intel or Qualcomm or any other specific family of hardware – you simply call the Windows ML APIs, feed in a properly formatted model, and we just take care of the rest, automatically provisioning everything needed on the target hardware and keeping everything up to date.

This will greatly simplify the dependencies you have to manage and worry about.

What makes all of this possible is the deep level of interaction we've had with our amazing hardware partners: Qualcomm, Intel, AMD, and Nvidia. They will continue to provide execution providers for WinML, and submit them to Microsoft when they have updates or new silicon they're introducing into the market.

Microsoft will certify these new execution providers to ensure there are no regressions in inferencing accuracy, and then we'll take on the responsibility of deploying the EPs to target machines on the hardware vendors' behalf, making it super easy for the Windows ML ecosystem as a whole to stay current & up-to-date.

This is a different approach from what technologies such as DirectML and DirectX take where Microsoft tries to abstract APIs over hardware ecosystem changes – with the new Windows ML, we're changing the landscape to empower hardware vendors to rapidly and directly introduce amazing new silicon with immediate 'day zero' execution provider support for that hardware as it arrives in market.

## Performance

Lastly, let's talk about performance. When we say “performance”, most people think about wall clock speed – and that's not surprising because a lot of AI workloads are computationally expensive. But performance is even more than just pure speed – as AI becomes ubiquitous in app experiences, we need a runtime that can optimize inference in a way that preserves battery life, and also maintains a high degree of accuracy so the AI produces good, accurate results.

AI workloads usually fall into one of two buckets: [1] ambient AI, where the AI is happening silently in the background users interact with your app, and [2] explicit AI where users know they've kicked off an AI task, which is usually some sort of genAI scenario.

Since Windows ML now supports NPUs as a first class citizen, ambient AI workloads can be offloaded to a dedicated processor with 40+ TOPS {check: current floor?} of processing power, and drawing power usually in the ones of watts. This means that Windows ML is perfect for ambient AI workloads – in most cases users of your apps will feel the magic of AI without having to wait or stress about their PC's battery life.

Not every machine has an NPU and a lot of computationally heavy AI tasks can be best served by a dedicated GPU. In the past, Windows ML relied on a DirectML EP to handle GPU workloads, adding to the number of layers between your model and the silicon. We've stripped away this DirectML layer, and now Windows ML works directly with dedicated execution providers for the GPU, giving you to-the-metal performance on par with dedicated SDKs of the past like TensorRT, ROCm, AI Engine Direct, and Intel's Extension for PyTorch. In other words, we've engineered it to have best-in-class GPU performance, while retaining the write-once-run-anywhere benefits that the past DirectML-based solution offered.

In both of these cases, performance really matters – and when we say performance, we're not just talking about wallclock speed… pure speed matters, but so does battery life, and also accuracy, so the user gets actually-good results.

This opens up a range of AI-powered experiences and scenarios; you can run ambient AI workloads and agents on dedicated NPUs, or run workloads on integrated GPUs to keep the discrete GPU free if needed. And if you just want raw power, you can target today's modern dGPUs to run heavier workloads as fast as possible.

## Samples

Don't just take our word for it. Download and run our samples linked below to begin trying it out for yourself today.
