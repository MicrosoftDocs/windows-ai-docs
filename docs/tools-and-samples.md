---
author: eliotcowley
title: Tools and samples
description: Learn about the different tools and samples available for Windows Machine Learning.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, samples, tools
ms.localizationpriority: medium
---

# Tools and samples

The [Windows-Machine-Learning repository on GitHub](https://github.com/Microsoft/Windows-Machine-Learning) contains sample applications that demonstrate how to use Windows Machine Learning, as well as tools that help verify models and troubleshoot issues during development.

## Tools

The following tools are available on GitHub.

| Name | Description |
|------|-------------|
| [WinMLRunner](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLRunner) | A command-line tool that can run .onnx or .pb models where the input and output variables are tensors or images. Useful because it lets you run the WinML APIs against a model and see if it hits any issues before trying to integrate it into an application. |

## Samples

The following sample applications are available on GitHub.

| Name | Description |
|------|-------------|
| [Custom Tensorization (Win32 C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization) | Shows how to tensorize an input image by using the WinML APIs on both the CPU and GPU. |
| [FNS Style Transfer (UWP C#)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/FNSCandyStyleTransfer) | Uses the FNS-Candy style transfer model to re-style images or video streams. |
| [MNIST (UWP C#/C++)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/MNIST) | Corresponds to [Tutorial: Create a Windows Machine Learning UWP application (C#)](get-started-uwp.md). Start from a basis and work through the tutorial, or run the completed project. |
| [SqueezeNet Object Detection (Win32 C++, UWP C#/JavaScript)](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/SqueezeNetObjectDetection) | Uses SqueezeNet, a pre-trained machine learning model, to detect the predominant object in an image selected by the user from a file. |

[!INCLUDE [help](includes/get-help.md)]