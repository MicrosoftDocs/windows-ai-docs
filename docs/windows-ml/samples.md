---
title: Windows Machine Learning samples
description: Learn about the different samples available for Windows Machine Learning.
ms.date: 5/20/2020
ms.topic: article
keywords: windows 10, windows machine learning, WinML, samples, tools
ms.localizationpriority: medium
---

# Windows Machine Learning samples

The [Windows-Machine-Learning repository on GitHub](https://github.com/Microsoft/Windows-Machine-Learning) contains sample applications that demonstrate how to use Windows Machine Learning, as well as tools that help verify models and troubleshoot issues during development.

## Samples

The following sample applications are available on GitHub.

| Name | Description |
|------|-------------|
| [AdapterSelection (Win32 C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/AdapterSelection/AdapterSelection/cpp) | A desktop application that demonstrates how to choose a specific device adapter for running your model. |
 [BatchSupport](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/BatchSupport) | Shows how to bind and evaluate batches of inputs with Windows ML. |
| [Custom Operator Sample (Win32 C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomOperatorCPU/desktop/cpp) | A desktop application that defines multiple custom CPU operators. One of these is a debug operator that you can integrate into your own workflow. |
| [Custom Tensorization (Win32 C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization) | Shows how to tensorize an input image by using the Windows ML APIs on both the CPU and GPU. |
| [Custom Vision (UWP C#)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml) | Shows how to train an ONNX model in the cloud using Custom Vision, and integrate it into an application with Windows ML. |
| [Emoji8 (UWP C#)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/Emoji8/UWP/cs) | Shows how you can use Windows ML to power a fun emotion-detecting application. |
| [FNS Style Transfer (UWP C#)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/FNSCandyStyleTransfer) | Uses the FNS-Candy style transfer model to re-style images or video streams. |
| [MNIST (UWP C#/C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/MNIST) | Corresponds to [Tutorial: Create a Windows Machine Learning UWP application (C#)](get-started-uwp.md). Start from a basis and work through the tutorial, or run the completed project. |
| [NamedDimensionOverrides](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/NamedDimensionOverrides) | Demonstrates how to override named dimensions to concrete values in order to optimize model performance. |
| [PlaneIdentifier (UWP C#, WPF C#)](https://github.com/Microsoft/Windows-AppConsult-Samples-UWP/tree/master/PlaneIdentifier) | Uses a pre-trained machine learning model, generated using the Custom Vision service on Azure, to detect if the given image contains a specific object: a plane. |
| [RustSqueezeNet](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/RustSqueezenet) | Rust projection of WinRT using SqueezeNet. |
| [SqueezeNet Object Detection (Win32 C++, UWP C#/JavaScript, .NET5, .NETCORE)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/SqueezeNetObjectDetection) | Uses SqueezeNet, a pre-trained machine learning model, to detect the predominant object in an image selected by the user from a file. |
| [SqueezeNet Object Detection (Azure IoT Edge on Windows, C#)](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/EdgeModules/SqueezeNetObjectDetection/cs) | This is a sample module showing how to run Windows ML inferencing in an Azure IoT Edge module running on Windows. Images are supplied by a connected camera, inferenced against the SqueezeNet model, and sent to IoT Hub. |
| [StreamFromResource](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/StreamFromResource/StreamFromResource) | Shows how to take an embedded resource that contains an ONNX model and convert it to a stream that can be passed to the LearningModel constructor. |
| [StyleTransfer](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/StyleTransfer) (C#) | A UWP app which performs style transfer on user-provided input images or web camera streams. |
| [winml_tracker (ROS C++)](https://github.com/ms-iot/winml_tracker) | A ROS (Robot Operating System) node which uses Windows ML to track people (or other objects) in camera frames. |

[!INCLUDE [help](../includes/get-help.md)]
