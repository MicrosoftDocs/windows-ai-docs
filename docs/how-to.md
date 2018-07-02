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

# How to develop with Windows ML

![windows ML developer flow](images/winmlstory.png)

## ONNX models

:::row:::
    :::column:::
       Windows ML evaluates models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools. Windows ML supports the [1.2.2 release](https://github.com/onnx/onnx/tree/rel-1.2.2) of ONNX.
    :::column-end:::
    :::column:::
        ![ONNX](images/onnx.png)
    :::column-end:::
:::row-end:::

[Get an ONNX model](get-onnx-model.md) or [Convert a model to ONNX](conversion-samples.md).

## Load, bind, and evaluate

[Add the model with mlgen](mlgen.md) or [Use the Windows ML APIs](winml-api.md).