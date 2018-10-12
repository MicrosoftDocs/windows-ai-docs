---
author: rosanevallim
title: FAQ (Frequently Asked Questions)
description: This page contains answers to the most popular questions from the community.
ms.author: rovalli
ms.date: 10/12/2018
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
If you already have a model and want to convert it to ONNX, we recommend installing the latest version of the [winmltools](convert-model-winmltools.md) package, which supports the 1.2.2 format.

## Which version of winmltools should I use?

We always recommend you download and install the latest version of the **winmltools** package. This will ensure you can create ONNX models that target the latest versions of Windows.

## Which version of Visual Studio should I use in order to get automatic code generation (mlgen)?

The minimum recommended version of [Visual Studio](https://visualstudio.microsoft.com/vs/) with support for [mlgen](mlgen.md) is 15.8.7.

## Can I use onnxmltools instead of winmltools?

Yes, you can, but you will need to make sure you install the correct version of [onnxmltools](https://github.com/onnx/onnxmltools) in order to target ONNX v1.2.2, which is the minimum ONNX version supported by Windows ML. If you are unsure of which version to install, we recommend installing the latest version of **winmltools** instead. This 
will ensure you will be able to target the ONNX version supported by Windows.

## Why can't I load a model?

There are several reasons why you might have trouble loading a model, but one of the most common ones when developing on UWP is due to file access restrictions. By default, UWP applications can only access certain parts of the file system, and require user permission or extra capabilities in order to access other locations. See [File access permissions](https://docs.microsoft.com/windows/uwp/files/file-access-permissions) for more information.

[!INCLUDE [help](includes/get-help.md)]