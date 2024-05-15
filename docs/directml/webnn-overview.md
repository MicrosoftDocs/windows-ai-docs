---
title: WebNN Overview 
description: The Web Neural Network API (WebNN) is an emerging web standard that allows web apps and frameworks to accelerate deep neural networks with on-device hardware.
ms.topic: article
ms.date: 05/22/2024
author: quinnradich
ms.author: quradic
---

# WebNN Overview

The Web Neural Network (WebNN) API is an emerging web standard that allows web apps and frameworks to accelerate deep neural networks with GPUs, CPUs, or purpose-built AI accelerators such as NPUs. The WebNN API leverages the DirectML API on Windows to access the native hardware capabilities and optimize the execution of neural network models. 

As the use of AI/ML in apps become more popular, the WebNN API provides the following benefits: 

* *Performance Optimizations* – By utilizing DirectML, WebNN enables web apps and frameworks to take advantage of the best available hardware and software optimizations for each platform and device, without requiring complex and platform-specific code. 
* *Low Latency* - In-browser inference enables novel use cases with local media sources, such as real-time video analysis, face detection, and speech recognition, without the need to send data to remote servers and wait for responses. 
* *Privacy Preservation* - User data stays on-device and preserves user-privacy, as web apps and frameworks do not need to upload sensitive or personal information to cloud services for processing. 
* *High Availability* - No reliance on the network after initial asset caching for offline case, as web apps and frameworks can run neural network models locally even when the internet connection is unavailable or unreliable. 
* *Low Server Cost* - Computing on client devices means no servers needed, so web apps can reduce the operational and maintenance costs of running AI/ML services in the cloud. 

AI/ML supported by WebNN include generative AI, person detection, face detection, semantic segmentation, skeleton detection, style transfer, super resolution, image captioning, machine translation, and noise suppression.

> [!NOTE]
> The WebNN API is still in progress, with GPU support in a preview state and NPU support coming soon. The WebNN API should not currently be used in a production environment.

## Framework support

WebNN is designed as a backend API for web frameworks. For Windows, we recommend using [ONNX Runtime Web](https://onnxruntime.ai/docs/tutorials/web/). This gives a familiar experience to using DirectML and ONNX Runtime natively so you can have a consistent experience deploying AI in ONNX format across web and native applications.

## WebNN requirements

You can check information about your browser by navigating to about://version in your chromium browser's address bar.

| Hardware | Web Browsers | Windows version | ONNX Runtime Web version | Driver Version |
| --- | --- | --- | --- | --- | 
| **GPU** | WebNN requires a Chromium browser*. Please use the most recent version of Microsoft Edge Beta. | Minimum version: Windows 11, version 21H2. |Minimum version: 1.18 | Install the latest driver for your hardware. | 
| **NPU** | WebNN requires a Chromium browser*. Please use the most recent version of Microsoft Edge Canary. | Minimum version: TBD. |Minimum version: 1.18 | Intel driver version: [32.0.100.2381.](https://www.intel.com/content/www/us/en/download/794734/intel-npu-driver-windows.html) See FAQ for steps on how to update the driver. |

![Diagram of the structure behind integrating WebNN into your web app](images/webnn-diagram.png)

> [!NOTE]
> WebNN NPU-support is currently limited to Intel’s® Core™ Ultra processors with Intel® AI Boost.

> [!NOTE]
> Chromium based browsers can currently support WebNN, but will depend on the individual browser's implementation status.

## Model support 

### GPU (Preview):
When running on GPUs, WebNN currently supports the following models:

* [Stable Diffusion Turbo](https://microsoft.github.io/webnn-developer-preview/demos/sd-turbo/)
* [Stable Diffusion 1.5](https://microsoft.github.io/webnn-developer-preview/demos/stable-diffusion-1.5/)
* [Whisper-base](https://microsoft.github.io/webnn-developer-preview/demos/whisper-base/)
* [MobileNetv2](https://microsoft.github.io/webnn-developer-preview/demos/image-classification/)
* [Segment Anything](https://microsoft.github.io/webnn-developer-preview/demos/segment-anything/)
* ResNet 
* EfficientNet 
* SqueezeNet 

WebNN also works with custom models as long as operator support is sufficient. Check status of operators [here](https://webmachinelearning.github.io/webnn-status/).

### NPU (Coming Soon):
On Intel’s® Core™ Ultra processors with Intel® AI Boost NPU, WebNN aims to support: 

* Whisper-base 
* MobileNetv2 
* ResNet 
* ESRGAN 
* EfficientNet 

## FAQ

#### **How do I file an issue with WebNN?**

For general issues with WebNN, please file an issue on our [WebNN Developer Preview GitHub](https://github.com/microsoft/webnn-developer-preview/issues)

For issues with ONNX Runtime Web or the WebNN Execution Provider, go to the [ONNXRuntime Github](https://github.com/microsoft/onnxruntime/issues).

#### **How do I debug issues with WebNN?**

The [WebNN W3C Spec](https://www.w3.org/TR/webnn/) has information on error propagation, typically through DOM exceptions. The log at the end of about://gpu may also have helpful information. For further issues please file an issue as linked above.

#### Does WebNN support other operating systems?

Currently, WebNN best supports the Windows operating system. A version for Mac operating systems is in progress.

#### What hardware back-ends are currently available? Are certain models only supported with specific hardware back-ends?

You can find information about operator support in WebNN at [Implementation Status of WebNN Operations | Web Machine Learning](https://webmachinelearning.github.io/webnn-status/).

#### What are the steps to update the Intel driver for NPU Support (Coming Soon)?
1. Uncompress the ZIP file.
2. Press Win+R to open the Run dialog box.
3. Type devmgmt.msc into the text field.
4. Press Enter or click OK.
5. In the Device Manager, open the "Neural processors" node
6. Right click on the NPU who's driver you wish to update.
7. Select "Update Driver" from the context menu
8. Select "Browse my computer for drivers"
9. Select "Let me pick from a list of available drivers on my computer"
10. Press the "Have disk" button
11. Press the "Browse" button
12. Navigate to the place where you decompressed the aforementioned zip file.
13. Press OK.  
 


