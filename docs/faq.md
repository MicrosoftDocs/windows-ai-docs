---
author: rosanevallim
title: FAQ (Frequently Asked Questions)
description: This page contains answers to the most popular questions from the community.
ms.author: rovalli
ms.date: 11/7/2018
ms.topic: article
ms.prod: windows
ms.technology: desktop
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# FAQ (Frequently Asked Questions)

This page contains answers to the most popular questions from the community.

## How do I know if the ONNX model I have will run with Windows ML?

The minimum ONNX version supported by the Windows ML API is 1.2.2. When training a model, check if your framework supports saving models to the 1.2.2 format.

## How do I convert a model of a different format to ONNX?

You can use [WinMLTools](convert-model-winmltools.md) to convert models of several different formats to ONNX.

## Which version of WinMLTools should I use?

We always recommend you download and install the latest version of the **winmltools** package. This will ensure you can create ONNX models that target the latest versions of Windows.

## Can I use onnxmltools instead of winmltools?

Yes, you can, but you will need to make sure you install the correct version of [onnxmltools](https://github.com/onnx/onnxmltools) in order to target ONNX v1.2.2, which is the minimum ONNX version supported by Windows ML. If you are unsure of which version to install, we recommend installing the latest version of **winmltools** instead. This 
will ensure you will be able to target the ONNX version supported by Windows.

## Which version of Visual Studio should I use in order to get automatic code generation (mlgen)?

The minimum recommended version of [Visual Studio](https://visualstudio.microsoft.com/vs/) with support for [mlgen](mlgen.md) is 15.8.7.

## Why can't I load a model?

There are several reasons why you might have trouble loading a model, but one of the most common ones when developing on UWP is due to file access restrictions. By default, UWP applications can only access certain parts of the file system, and require user permission or extra capabilities in order to access other locations. See [File access permissions](https://docs.microsoft.com/windows/uwp/files/file-access-permissions) for more information.

## What does WinML run on by default?

If you don't specify a device to run on with [LearningModelDeviceKind](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevicekind), or if you use **LearningModelDeviceKind.Default**, the system will decide which device will evaluate the model. This is usually the CPU. To make WinML run on the GPU, specify one of the following values when creating the [LearningModelDevice](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevice):

* **LearningModelDeviceKind.DirectX**
* **LearningModelDeviceKind.DirectXHighPerformance**
* **LearningModelDeviceKind.DirectXMinPower**

[!INCLUDE [help](includes/get-help.md)]
