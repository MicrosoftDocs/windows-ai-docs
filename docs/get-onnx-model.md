---
author: serenaz
title: Automatic code generation with mlgen
description:
ms.author: sezhen
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Get ONNX models for Windows ML

Windows ML evaluates models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format ([version 1.2.2](https://github.com/onnx/onnx/tree/rel-1.2.2)). ONNX is an open format for ML models, allowing you to interchange models between various [ML frameworks and tools](http://onnx.ai/supported-tools).

To get an ONNX model to use with Windows ML, you can:

- Download a pre-trained ONNX model from the [ONNX Models repo](https://github.com/onnx/models) or [Azure AI Gallery](https://gallery.azure.ai/browse?winml=true).
- [Train your own model](train-model-custom-vision.md) with Azure Custom Vision or VS Tools for AI, and export to ONNX format.
- Convert models trained in other frameworks into ONNX format with [WinMLTools](convert-model-winmltools.md) converters or [ONNX tutorials](https://github.com/onnx/tutorials).

Once you have an ONNX model, you'll integrate the model into your app's code with [mlgen](mlgen.md) or the [Windows ML APIs](integrate-model.md). Then, you'll be able use machine learning in your Windows apps and devices!