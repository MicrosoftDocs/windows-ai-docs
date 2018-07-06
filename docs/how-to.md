---
author: serenaz
title: How to develop with Windows ML
description: How-to articles for developing with Windows ML.
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, windows machine learning
ms.localizationpriority: medium
---

# How-to guide

The articles in this section detail how to develop with Windows ML.

![Windows ML developer process](images/winmlstory.png)

## 1. ONNX models

First, you'll need a trained ML model in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format to use Windows ML. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools. Windows ML supports the [1.2.2 release](https://github.com/onnx/onnx/tree/rel-1.2.2) of ONNX.

You can either:

- Get an ONNX model from the [ONNX Model Zoo](https://github.com/onnx/models) or [Azure AI Gallery](https://gallery.azure.ai/models).
- Train an ONNX model with [VS Tools for AI](train-ai-model.md), [CustomVision.ai](https://customvision.ai/), [CNTK](https://www.microsoft.com/cognitive-toolkit/), Pytorch or Caffe2. For a complete and updated list of frameworks, please see [ONNX's supported tools](https://onnx.ai/supported-tools).
- Convert your own model trained in another framework into ONNX format with [WinMLTools](conversion-samples.md).

## 2. Integrate the model

To integrate your ONNX model(s) in your app, you can either:

- [Add the model with mlgen](mlgen.md), Windows ML's code generator.
- [Use the Windows ML APIs](winml-api.md) directly in your app.

## See also

- [Tutorials](tutorials.md)
- [Code samples](https://github.com/Microsoft/Windows-Machine-Learning)