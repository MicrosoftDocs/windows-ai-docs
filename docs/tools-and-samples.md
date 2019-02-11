---
author: eliotcowley
title: Tools and samples
description: Learn about the different tools and samples available for Windows Machine Learning.
ms.author: elcowle
ms.date: 2/11/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, samples, tools
ms.localizationpriority: medium
---

# Tools and samples

The [Windows-Machine-Learning repository on GitHub](https://github.com/Microsoft/Windows-Machine-Learning) contains sample applications that demonstrate how to use Windows Machine Learning, as well as tools that help verify models and troubleshoot issues during development.

## Tools

The following tools are available on GitHub.

| Name | Description |
|------|-------------|
| [WinML Code Generator (mlgen)](https://marketplace.visualstudio.com/items?itemName=WinML.mlgen) | A Visual Studio extension to help you get started using WinML APIs on UWP apps by generating template code when you add a trained ONNX file into the UWP project. Available for [Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=WinML.mlgen) and [2019](https://marketplace.visualstudio.com/items?itemName=WinML.MLGenV2). See the [documentation](mlgen.md) for more information.
| [WinML Dashboard (Preview)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) | A GUI-based tool for viewing, editing, converting, and validating machine learning models for Windows ML's inference engine. |
| [WinMLRunner](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLRunner) | A command-line tool that can run .onnx or .pb models where the input and output variables are tensors or images. Useful because it lets you run the WinML APIs against a model and see if it hits any issues before trying to integrate it into an application. |
| [WinMLTools](https://pypi.org/project/winmltools/) | A Python package that provides model conversion from different ML toolkits into ONNX, as well as a quantization tool to reduce the memory footprint of a model. |

## Samples

The following sample applications are available on GitHub.

| Name | Description |
|------|-------------|
| [AdapterSelection (Win32 C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/AdapterSelection/AdapterSelection/cpp) | A desktop application that demonstrates how to choose a specific device adapter for running your model. |
| [Custom Operator Sample (Win32 C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomOperatorCPU/desktop/cpp) | A desktop application that defines multiple custom CPU operators. One of these is a debug operator that you can integrate into your own workflow. |
| [Custom Tensorization (Win32 C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization) | Shows how to tensorize an input image by using the WinML APIs on both the CPU and GPU. |
| [Emoji8 (UWP C#)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/Emoji8/UWP/cs) | Shows how you can use WinML to power a fun emotion-detecting application. |
| [FNS Style Transfer (UWP C#)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/FNSCandyStyleTransfer) | Uses the FNS-Candy style transfer model to re-style images or video streams. |
| [MNIST (UWP C#/C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/MNIST) | Corresponds to [Tutorial: Create a Windows Machine Learning UWP application (C#)](get-started-uwp.md). Start from a basis and work through the tutorial, or run the completed project. |
| [PlaneIdentifier (UWP C#, WPF C#)](https://github.com/Microsoft/Windows-AppConsult-Samples-UWP/tree/master/PlaneIdentifier) | Uses a pre-trained machine learning model, generated using the Custom Vision service on Azure, to detect if the given image contains a specific object: a plane. |
| [SqueezeNet Object Detection (Win32 C++, UWP C#/JavaScript)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/SqueezeNetObjectDetection) | Uses SqueezeNet, a pre-trained machine learning model, to detect the predominant object in an image selected by the user from a file. |
| [winml_tracker (ROS C++)](https://github.com/ms-iot/winml_tracker) | A ROS (Robot Operating System) node which uses WinML to track people (or other objects) in camera frames. |

[!INCLUDE [help](includes/get-help.md)]
