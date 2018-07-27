---
author: serenaz
title: Windows ML Overview
description: How-to develop with Windows ML.
ms.author: sezhen
ms.date: 08/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows ML overview

To use Windows ML, you'll get an ONNX model, add it to your app, and integrate it into your code with the Windows ML APIs or tools.

![windows ml developer flow](images/winml-flow.png)

## ONNX models

You'll need a trained ML model in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format to use Windows ML. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools. Windows ML supports the [1.2.2 release](https://github.com/onnx/onnx/tree/rel-1.2.2) of ONNX.

You can either:

- Get an ONNX model from the [ONNX Model Zoo](https://github.com/onnx/models) or [Azure AI Gallery](https://gallery.azure.ai/models).
- Train an ONNX model with [CustomVision.ai](https://customvision.ai/), [CNTK](https://www.microsoft.com/cognitive-toolkit/), or any training framework in [ONNX's supported tools](https://onnx.ai/supported-tools).
- Convert a model trained in another framework into ONNX format with [WinMLTools](winmltools.md).

## Integrate the model

To integrate your ONNX model(s) in your app, you can either:

- Add the model with [mlgen](mlgen.md), Windows ML's code generator.
- Use the [Windows ML APIs](winml-api.md) directly in your app.

## See also

- [Code samples](https://github.com/Microsoft/Windows-Machine-Learning)
