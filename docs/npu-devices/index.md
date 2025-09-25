---
title: Copilot+ PCs developer guide
description: Developer guide for Windows Copilot+ PCs.
ms.topic: article
ms.date: 09/24/2025
no-loc: [Copilot]
#customer intent: As a Windows developer, I want to learn how to develop AI applications that take advantage of the NPU in Copilot+ PCs to deliver high-performance AI experiences with improved battery life.
---

# Develop AI applications for Copilot+ PCs

Copilot+ PCs are a new class of Windows 11 hardware powered by a high-performance Neural Processing Unit (NPU) — a specialized computer chip for AI-intensive processes like real-time translations and image generation—that can perform more than 40 trillion operations per second (TOPS). Copilot+ PCs provide all–day battery life and access to the most advanced AI features and models. 

Learn more:

- [Empowering the future: The expanding Arm app ecosystem for Copilot+ PCs](https://blogs.windows.com/windowsdeveloper/2025/09/18/empowering-the-future-the-expanding-arm-app-ecosystem-for-copilot-pcs/)
- [Introducing Copilot+ PCs - The Official Microsoft Blog](https://blogs.microsoft.com/blog/2024/05/20/introducing-copilot-pcs/).

The following Copilot+ PC Developer Guidance covers:

- Device Prerequisites
- What is the Arm-based Snapdragon Elite X+ chip?
- Unique AI features supported by Copilot+ PCs with an NPU processor
- How to access the NPU on a Copilot+ PC
- How to use ONNX Runtime to programmatically access the NPU on a Copilot+ PC
- How to measure performance of AI models running locally on the device NPU

## Prerequisites

This guidance is specific to [Copilot+ PCs](https://www.microsoft.com/windows/copilot-plus-pcs).

Many of the new Windows AI features require an NPU with the ability to run at 40+ TOPS, including but not limited to:

- Microsoft Surface Laptop Copilot+ PC
- Microsoft Surface Pro Copilot + PC
- HP OmniBook X 14
- Dell Latitude 7455, XPS 13, and Inspiron 14
- Acer Swift 14 AI
- Lenovo Yoga Slim 7x and ThinkPad T14s
- Samsung Galaxy Book4 Edge
- ASUS Vivobook S 15 and ProArt PZ13
- [Copilot+ PCs with new AMD and Intel silicon](https://blogs.windows.com/windowsexperience/2024/09/03/copilot-pcs-expand-availability-with-new-amd-and-intel-silicon), including [AMD Ryzen AI 300 series](https://www.amd.com/en/products/processors/consumer/ryzen-ai.html) and [Intel Core Ultra 200V series](https://www.intel.com/content/www/us/en/newsroom/news/core-ultra-200v-series-mobile.html).

Surface Copilot+ PCs for Business:

- [Surface Laptop for Business with Intel Core Ultra processors (Series 2)](https://www.microsoft.com/surface/business/surface-laptop-intel-7th-edition) (Available starting  Feb. 18, 2025)
- [Surface Pro for Business with Intel Core Ultra processors (Series 2)](https://www.microsoft.com/en-us/surface/business/surface-pro-intel-11th-edition) (Available starting Feb. 18, 2025)
- [Introducing new Surface Copilot+ PCs for Business](https://blogs.windows.com/devices/2025/01/30/introducing-new-surface-copilot-pcs-for-business/)

## What is the Arm-based Snapdragon Elite X chip?

The new Snapdragon X Elite Arm-based chip built by Qualcomm emphasizes AI integration through its industry-leading Neural Processing Unit (NPU). This NPU is able to process large amounts of data in parallel, performing trillions of operations per second, using energy on AI tasks more efficiently than a CPU or GPU resulting in longer device battery life. The NPU works in alignment with the CPU and GPU. Windows 11 assigns processing tasks to the most appropriate place in order to deliver fast and efficient performance. The NPU enables on-device AI intelligent experiences with Enterprise-grade security for enhanced protection from chip to cloud.

- Learn more about the [Qualcomm Snapdragon X Elite](https://www.qualcomm.com/products/mobile/snapdragon/pcs-and-tablets/snapdragon-x-elite).
- Learn more about using and developing for [Windows on Arm](/windows/arm/overview).

## Unique AI features supported by Copilot+ PCs with an NPU

Copilot+ PCs offer unique AI experiences that ship with modern versions of Windows 11. These AI features, designed to run on the device NPU, ship in the latest releases of Windows and will be available via APIs in the [Windows AI Foundry](../overview.md). Learn more about [Windows Runtime Copilot APIs](../apis/index.md) that are supported by models optimized to run (inference) on the NPU. These APIs will ship in an upcoming release of the [Windows App SDK](/windows/apps/windows-app-sdk/).

## How to access the NPU on a Copilot+ PC

The [Neural Processing Unit (NPU)](https://support.microsoft.com/windows/all-about-neural-processing-units-npus-e77a5637-7705-4915-96c8-0c6a975f9db4) is a new hardware resource. Like other hardware resources on a PC, the NPU needs software to be specifically programmed to take advantage of the benefits it offers. NPUs are designed specifically to execute the deep learning math operations that make up AI models.

The Windows 11 Copilot+ AI features mentioned above have been specifically designed to take advantage of the NPU. Users will get improved battery life and faster inference execution time for AI models that target the NPU. Windows 11 support for NPUs will include Arm-based Qualcomm devices, as well as Intel and AMD devices (coming soon).

For devices with NPUs, the [Task Manager](/shows/inside/task-manager) can now be used to view NPU resource usage.

![Screenshot of Windows Task Manager displaying NPU performance alongside CPU, GPU, Memory, Ethernet, and Disk](../images/task-manager-npu.png)

The recommended way to inference (run AI tasks) on the device NPU is to use [Windows ML](#how-to-programmatically-access-the-npu-on-a-copilot-pc-for-ai-acceleration).

## How to programmatically access the NPU on a Copilot+ PC for AI acceleration

The recommended way to programmatically access the NPU (Neural Processing Unit) and GPU for AI acceleration has shifted from DirectML to Windows ML (WinML). This transition reflects a broader effort to simplify and optimize the developer experience for AI workloads on Windows devices. You can find updated guidance here: [Learn how Windows Machine Learning (ML) helps your Windows apps run AI models locally.](../new-windows-ml/overview.md).

- **Built-in EP discovery**: Previously, developers were required to know which Execution Providers (EPs) were compatible with their hardware and bundle those EPs with their applications. This often led to larger application sizes and increased complexity in managing dependencies. With Windows ML, the process is now automated and streamlined. Windows ML automatically detects the available hardware on the device and downloads the appropriate EPs as needed. This means that developers no longer need to bundle specific EPs with their applications, resulting in smaller application sizes and reduced complexity.

- **Integrated EP delivery**: The required EPs, such as Qualcomm’s QNNExecutionProvider or Intel’s OpenVINO EP, are now bundled with Windows or delivered via Windows Update, eliminating the need for manual downloads.

- **ORT under the hood**: Windows ML still uses ONNX Runtime as its inference engine, but abstracts away the complexity of EP management. [ONNX Runtime](https://onnxruntime.ai/) is an open source inference and training engine for AI models using the ONNX format and enabling developers to build AI applications that can run efficiently across a wide range of devices.

- **Collaboration with hardware vendors**: Microsoft works directly with hardware vendors, such as Qualcomm and Intel, to ensure EP compatibility with early driver versions and new silicon (for example, Snapdragon X Elite, Intel Core Ultra, etc.).

When you deploy an AI model using Windows ML on a Copilot+ PC:

- Windows ML queries the system for available hardware accelerators.
- It selects the most performant EP (for example, QNN for Qualcomm NPUs, OpenVINO for Intel NPUs).
- The EP is loaded automatically, and inference begins.
- If the preferred EP fails or is unavailable, Windows ML gracefully falls back to another (for example, using the GPU or CPU).

This means developers can focus on building AI experiences without worrying about low-level hardware integration

### Supported model formats

AI models are often trained and available in larger data formats, such as FP32. Many NPU devices, however, only support integer math in lower bit format, such as INT8, for increased performance and power efficiency. Therefore, AI models need to be converted (or "quantized") to run on the NPU. There are many models available that have already been converted into a ready-to-use format. You can also *bring your own model* (BYOM) to convert or optimize.

- **[Qualcomm AI Hub (Compute)](https://aihub.qualcomm.com/compute/models)**: Qualcomm provides AI models that have already been validated for use on Copilot+ PCs with Snapdragon X Elite with the available models specifically optimized to run efficiently on this NPU. Learn more: [Accelerate model deployment with Qualcomm AI Hub | Microsoft Build 2024](https://www.youtube.com/watch?v=gh0HvhLWrUQ).
- **[ONNX Model Zoo](https://github.com/onnx/models)**: This open source repository offers a curated collection of pre-trained, state-of-the-art models in the ONNX format. These models are recommended for use with NPUs across all Copilot+ PCs, including Intel and AMD devices (coming soon).

For those who want to bring your own model, we recommend using the hardware-aware model optimization tool, [Olive](https://onnxruntime.ai/docs/performance/olive.html). Olive can help with model compression, optimization, and compilation to work with ONNX Runtime as an NPU performance optimization solution. Learn more: [AI made easier: How the ONNX Runtime and Olive toolchain will help you Q&A | Build 2023](https://www.youtube.com/watch?v=7_0N1VL5ZmA).

## How to measure performance of AI models running locally on the device NPU

To measure the performance of AI feature integration in your app and the associated AI model runtimes:

- **Record a trace**: Recording device activity over a period of time is known as system tracing. System tracing produces a "trace" file that can be used to generate a report and help you identify how to be improve your app's performance. Learn more: [Capture a system trace to analyze memory usage](/windows/apps/performance/disk-memory#capture-a-system-trace-to-analyze-memory-usage).

- **View NPU usage**: Examine which processes are using the NPU and the callstacks submitting work.

- **View work and callstacks on the CPU**: Examine the results of the pre-work feeding AI models and post-work processing AI models.

- **Load and Runtime**: Examine the length of time to load an AI model and create an ONNX Runtime session.

- **Runtime parameters**: Examine ONNX Runtime configuration and Execution Provider (EP) parameters that affect model runtime performance and optimization.

- **Individual inference times**: Track per-inference times and sub-details from the NPU.

- **Profiler**: Profile AI model operations to see how long each operator took to contribute to the total inference time.

- **NPU-specific**: Examine NPU sub-details such as sub-HW metrics, memory bandwidth, and more.

![Screenshot providing a general impression of the Windows Performance Analyzer tool](../images/npu-performance-analyzer.png)

To perform these measurements, we recommend the following diagnostic and tracing tools:

- **[Task Manager](/shows/inside/task-manager)**: Enables a user to view the performance of the Windows Operating System installed on their device, including Processes, Performance, App history, Startup apps, Users, Details, and Services. Real-time performance data will be shown for your device CPU, Memory, Storage Disk, Wi-Fi, GPU... and now NPU. Data includes the percentage of utilization, available memory, shared memory, driver version, physical location, and more.
- **[Windows Performance Recorder (WPR)](/windows-hardware/test/wpt/whats-new-in-the-windows-performance-toolkit-v11)**: WPR now ships with a Neural Processing profile to record NPU activity. This records [Microsoft Compute Driver Model (MCDM)](/windows-hardware/drivers/display/mcdm) interactions with the NPU. Developers can now see NPU usage, which processes are using the NPU, and the callstacks submitting work.
- **[Windows Performance Analyzer (WPA)](/windows-hardware/test/wpt/whats-new-in-the-windows-performance-toolkit-v11#windows-performance-analyzer)**: WPA creates graphs and data tables of Event Tracing for Windows (ETW) events that are recorded by Windows Performance Recorder (WPR), Xperf, or an assessment that is run in the Assessment Platform. It provides convenient access points for analyzing the CPU, Disk, Network, ONNX Runtime Events... and a new table for [NPU analysis](/windows-hardware/test/wpt/whats-new-in-the-windows-performance-toolkit-v11#npu-analysis), all in a single timeline. WPA can now view the work and callstacks on the CPU related to pre-work feeding AI models and post-work processing AI model results. [Download Windows Performance Analyzer from the Microsoft Store](https://apps.microsoft.com/detail/9n58qrw40dfw).
- [GPUView](/windows-hardware/drivers/display/installing-gpuview): GPUView is a development tool that reads logged video and kernel events from an event trace log (.etl) file and presents the data graphically to the user. This tool now includes both GPU and NPU operations, as well as support for viewing DirectX events for [MCDM](/windows-hardware/drivers/display/mcdm) devices like the NPU.
- **[ONNX Runtime events in Windows Performance Analyzer](https://onnxruntime.ai/docs/performance/tune-performance/logging_tracing.html)**: Starting with ONNXRuntime 1.17 (and enhanced in 1.18.1) the following use cases are available with events emitted in the runtime:
  - See how long it took to load an AI model and create an ONNX Runtime session.
  - See ONNX Runtime configuration and Execution Provider (EP) parameters that affect model runtime performance and optimization.
  - Track per inference times and sub-details from the NPU (QNN).
  - Profile AI Model operations to see how long each operator took to contribute to the total inference time.
  - Learn more about [ONNX Runtime Execution Provider (EP) Profiling](https://onnxruntime.ai/docs/performance/tune-performance/profiling-tools.html#execution-provider-ep-profiling).

> [!NOTE]
> WPR UI (the user interface available to support the command-line based WPR included in Windows), WPA, and GPUView are all part of Windows Performance Toolkit (WPT), version May 2024+. To use the WPT, you will need to: [Download the Windows ADK Toolkit](/windows-hardware/get-started/adk-install#download-the-adk-101261001-may-2024).

For a quickstart on viewing ONNX Runtime events with the Windows Performance Analyzer (WPA), follow these steps:

1. Download [ort.wprp](https://github.com/microsoft/onnxruntime/blob/main/ort.wprp) and [etw_provider.wprp](https://github.com/microsoft/onnxruntime/blob/main/onnxruntime/test/platform/windows/logging/etw_provider.wprp).

2. Open your command line and enter:

    ```powershell
    wpr -start ort.wprp -start etw_provider.wprp -start NeuralProcessing -start CPU
    echo Repro the issue allowing ONNX to run 
    wpr -stop onnx_NPU.etl -compress
    ```

3. Combine the Windows Performance Recorder (WPR) profiles with other [Built-in Recording Profiles](/windows-hardware/test/wpt/built-in-recording-profiles) such as CPU, Disk, etc.

4. [Download Windows Performance Analyzer (WPA) from the Microsoft Store](https://apps.microsoft.com/detail/9n58qrw40dfw).

5. Open the `onnx_NPU.etl` file in WPA. Double-Click to open these graphs:
    - "Neural Processing -> NPU Utilization
    - Generic Events for ONNX events

Additional performance measurement tools to consider using with the Microsoft Windows tools listed above, include:

- [Qualcomm Snapdragon Profiler](https://www.qualcomm.com/developer/software/snapdragon-profiler) (qprof): A GUI and system-wide performance profiling tool designed to visualize system performance, as well as identify optimization and application scaling improvement opportunities across Qualcomm SoC CPUs, GPUs, DSPs and other IP blocks. The Snapdragon Profiler allows viewing NPU sub-details, such as sub-HW metrics, memory bandwidth and more.

## Additional Resources

- [Windows AI Foundry overview](../overview.md)
- [Windows app performance and fundamentals overview](/windows/apps/performance/)
- [Windows on Arm overview](/windows/arm/overview)- [Empowering the future: The expanding Arm app ecosystem for Copilot+ PCs](https://blogs.windows.com/windowsdeveloper/2025/09/18/empowering-the-future-the-expanding-arm-app-ecosystem-for-copilot-pcs/)
- [What is Windows ML](../new-windows-ml/overview.md)
- [All about neural processing units (NPUs)](https://support.microsoft.com/windows/all-about-neural-processing-units-npus-e77a5637-7705-4915-96c8-0c6a975f9db4)
