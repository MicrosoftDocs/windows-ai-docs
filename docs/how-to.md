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

## ONNX models

To use Windows ML, you'll need a trained ML model in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools. Windows ML supports the [1.2.2 release](https://github.com/onnx/onnx/tree/rel-1.2.2) of ONNX.

You can either:

- [Get an ONNX model](get-onnx-model.md)
- [Convert existing models to ONNX](conversion-samples.md)

## Load, bind, evaluate

Once you have an ONNX model, you'll load, bind, and evaluate the model in your app.

You can either:

- [Add the model with mlgen](mlgen.md), which generates code that calls the Windows ML APIs for you.
- [Use the Windows ML APIs](winml-api.md) directly in your app.

## See also

- [Tutorials](tutorials.md)
- [Code samples](https://github.com/Microsoft/Windows-Machine-Learning)