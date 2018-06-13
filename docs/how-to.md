---
author: serenaz
title: 
description: 
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, windows machine learning
ms.localizationpriority: medium
---

# How to develop with Windows ML

In this overview, we'll cover how to use Windows ML to bring machine learning to your Windows 10 apps and devices.

## Video summary

> [!VIDEO https://www.youtube.com/embed/8MCDSlm326U]

## How to develop with Windows ML

![windows ML developer flow](images/winmlstory.png)

1. From any training environment, you'll a model in ONNX format.
2. Add the ONNX model file(s), and use Windows ML's automatic code generation, or call the Windows ML APIs.
3. Load, bind, and evaluate the model in your application code.
4. Run on any Windows 10 device!


## Windows ML APIs

The Windows ML APIs are WinRT APIs and consummable in both C# and C++. You can call the APIs from your application's code, and follow the [load, bind, evaluate](integrate-model.md) pattern to integrate your model into your app.

## Related topics

Windows ML basics:

- [Get an ONNX model](get-onnx-model.md)
- [Convert a model to ONNX](conversion-samples.md)
- [Integrate a model into your app](integrate-model.md)
