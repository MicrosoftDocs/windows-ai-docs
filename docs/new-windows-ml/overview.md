---
title: Introducing the new Windows Machine Learning (ML)
description: TBD
ms.topic: article
ms.date: 04/24/2025
---

# Introducing the new Windows Machine Learning (ML)

Windows ML originally debuted in April 2018. Since that time we've learned a lot about what it takes to ship real world AI experiences, both from the feedback we've received from external developers and from our own experiences shipping AI features like Windows Recall. 

We're releasing a Windows ML version that addresses real challenges faced every day by AI developers building on Windows.

**First**, there's the problem of hardware diversity.  One of the great things about the Windows ecosystem is that users can all pick whatever hardware best suits them!  But… as a developer trying to make AI experiences, it's hard for you to handle all that hardware diversity & choice.  We're seeing many of today's leading Windows apps building release roadmaps that focus on just one hardware vendor at a time, like “just Intel” or “just Qualcomm for now” or “we'll only work on discrete GPUs for the time being”, which greatly limits the number of devices their apps can even run on.

**Second**, there's the problem of deploying all the dependencies you need.  If you want to ship AI experiences in your app, then your app must ship & deploy three things:  a) the AI models you want to run, b) a runtime that allows you to inference those models, and c) vendor-specific tools and drivers that help whichever runtime you pick talk to the silicon.  And not only do you need to ship these things, but you need to maintain & update them too – so for example when a new version of the runtime comes out or a critical bug is fixed in it, you need to update your app with that runtime's fix.  As an app developer you must take ownership of all these dependencies – they becomes a part of your app, and the burden of maintaining everything falls on you.

**Third**, it's up to you to get the most out of the local hardware that our app is running on. Should your AI workloads run on CPU?  GPU?  NPU?  What if the processor is overloaded – how will you know and shift the workload to silicon that has more capacity?  If you're using different AI models, which ones run best on which processors?  This gets complex quickly, and it's up to you to write code that detects what's even available on the current device, and then tries to get the most performance out of it.  None of that is particularly easy to do.
An all new built from the ground up WinML Solves these problems the runtime is no longer needed to be inside your app, the EP is selected for your customers automatically depending on the hardware that is available to your customers. WinML will also manage your runtime dependencies, pushing the burden onto WinML and the Eps and out of your application. It will also help balance the load on the client device, choosing the appropriate hardware for AI load execution.

## Detailed overview

This new version of WinML will serve as the AI inferencing 'nucleus' of the Windows AI Foundry.  So, if you're using the Windows AI APIs to access the built-into-Windows models we offer, or are using our growing list of ready-to-use Foundry models with Foundry Local, you'll be running your AI workloads on Windows ML without even knowing it

However, if you're bringing your own models, or just need a high degree of fine-grained control over how model inference happens, you can use Windows ML directly via it's APIs.

This new Windows ML is built on a forked and specialized version of the ONNX runtime.  This allows us to make some Windows-specific enhancements to performance. We are also optimizing it around standard ONNX QDQ models, which enables a focus on getting the best inference performance on the local device without needed to enlarge the models unnecessarily.

The ONNX runtime talks to silicon via Execution Providers (EPs) which serve as a translation layer between the runtime and hardware drivers.  We've taken the execution provider work we did with Windows Recall and NPUs, combined that with new execution providers for GPUs, and wrapped that all into a single Windows ML framework that now fully delivers on the promise of enabling AI workloads that can target any hardware across CPU, GPU, and NPU: each type of processor is a first class citizen that is fully supported with the latest drivers and ONNX Runtime Execution Providers from the 4 major AI silicon vendors: Nvidia, AMD, Intel, and Qualcomm.  These processor types are now on equal footing – just write to Windows ML with ONNX QDQ models, and scale your AI workloads confidently across all types of hardware.

Windows ML isn't limited to only Copilot+ PCs – it will work on any supported Windows PC, which means you'll be able to target the hundreds of millions of Windows devices already in the market today that have powerful GPUs, but aren't Copilot+ PCs due to the lack of an NPU.  You can even use Windows ML on machines with only a CPU and iGPU, so long as you keep the workloads light enough for those processors to handle it.

Furthermore, not only does it work on all types of processors, but it will automatically keep itself up to date and adapt to future generations of CPUs, GPUs, and NPUs as they come out.  

When new hardware is released to market, Windows ML will update itself to use a new version of the execution provider, to provide day 1 support for that hardware as it comes to market.  This means that if your app uses WinML, it is future proofed to always run on the latest generation hardware in the Windows ecosystem.  

This will be made possible via a new certification program we're introducing, similar in spirit to driver certification, to ensure that as execution providers are created and updated, the accuracy of AI inference on those providers is maintained.

Once you reference WinML in your app code and install the app to a target customer's PC, a couple things will happen: 1) first, we'll download the latest version of WinML itself to ensure the runtime is properly installed alongside your app.  2) then, we'll also detect the hardware for the specific machine your app is installing to and download the appropriate driver and execution providers needed for that PC.

This means you don't need to carry your own execution providers in your app package, and in fact you don't even need to worry about execution providers at all, or shipping custom builds of AI runtimes that are specifically designed for Intel or Qualcomm or any other specific family of hardware – you simply call the Windows ML APIs, feed in a properly formatted model, and we just take care of the rest, automatically provisioning everything needed on the target hardware and keeping everything up to date.

This will greatly simplify the dependencies you have to manage and worry about.

What makes all of this possible is the deep level of interaction we've had with our amazing hardware partners: Qualcomm, Intel, AMD, and Nvidia. They will continue to provide execution providers for WinML, and submit them to Microsoft when they have updates or new silicon they're introducing into the market.  

Microsoft will certify these new execution providers to ensure there are no regressions in inferencing accuracy, and then we'll take on the responsibility of deploying the EPs to target machines on the hardware vendors' behalf, making it super easy for the Windows ML ecosystem as a whole to stay current & up-to-date.  

This is a different approach from what technologies such as DirectML and DirectX take where Microsoft tries to abstract APIs over hardware ecosystem changes – with the new Windows ML, we're changing the landscape to empower hardware vendors to rapidly and directly introduce amazing new silicon with immediate 'day zero' execution provider support for that hardware as it arrives in market.

## Performance

Lastly, let's talk about performance.  When we say “performance”, most people think about wall clock speed – and that's not surprising because a lot of AI workloads are computationally expensive.  But performance is even more than just pure speed – as AI becomes ubiquitous in app experiences, we need a runtime that can optimize inference in a way that preserves battery life, and also maintains a high degree of accuracy so the AI produces good, accurate results.  

AI workloads usually fall into one of two buckets: [1] ambient AI, where the AI is happening silently in the background users interact with your app, and [2] explicit AI where users know they've kicked off an AI task, which is usually some sort of genAI scenario.  

Since Windows ML now supports NPUs as a first class citizen, ambient AI workloads can be offloaded to a dedicated processor with 40+ TOPS {check: current floor?} of processing power, and drawing power usually in the ones of watts.  This means that Windows ML is perfect for ambient AI workloads – in most cases users of your apps will feel the magic of AI without having to wait or stress about their PC's battery life.

Not every machine has an NPU and a lot of computationally heavy AI tasks can be best served by a dedicated GPU.  In the past, Windows ML relied on a DirectML EP to handle GPU workloads, adding to the number of layers between your model and the silicon.  We've stripped away this DirectML layer, and now Windows ML works directly with dedicated execution providers for the GPU, giving you to-the-metal performance on par with dedicated SDKs of the past like TensorRT, ROCm, AI Engine Direct, and Intel's Extension for PyTorch.  In other words, we've engineered it to have best-in-class GPU performance, while retaining the write-once-run-anywhere benefits that the past DirectML-based solution offered.

In both of these cases, performance really matters – and when we say performance, we're not just talking about wallclock speed… pure speed matters, but so does battery life, and also accuracy, so the user gets actually-good results.

This opens up a range of AI-powered experiences and scenarios; you can run ambient AI workloads and agents on dedicated NPUs, or run workloads on integrated GPUs to keep the discrete GPU free if needed.  And if you just want raw power, you can target today's modern dGPUs to run heavier workloads as fast as possible.

## Samples

Don't just take our word for it. Download and run our samples linked below to begin trying it out for yourself today.
