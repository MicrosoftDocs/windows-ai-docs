---
author: serenaz
title: Get ONNX models for Windows ML
description: Get an ONNX model from a curated gallery, or train your own ONNX model for Windows ML.
ms.author: sezhen
ms.date: 
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, windows machine learning
ms.localizationpriority: medium
---

# Get ONNX models for Windows ML

To use Windows ML, you'll need a pre-trained machine learning model in the Open Neural Network Exchange (ONNX) format. 
To get an ONNX model, you have a few options. You can (1) browse pre-trained models from curated galleries (2) train your own ONNX model or (3) convert an existing model to ONNX. In this article we cover options 1 and 2, while the next one explains everything you need to know in order to convert existing models.

## 1. Pre-trained models from ONNX Galleries

:::row:::
    :::column:::
        ![ONNX Github screenshot](http://via.placeholder.com/1000x150)
        ### [ONNX Model Zoo](https://github.com/onnx/models)
    :::column-end:::
    :::column:::
        ![Azure ML Gallery screenshot](http://via.placeholder.com/1000x150)
        ### [Azure AI Gallery](https://gallery.azure.ai/models)
    :::column-end:::
:::row-end:::

## 2. Train your own ONNX model

When your scenario requires a custom model trained from scratch, you can do so by choosing a training framework which supports saving your models to the ONNX format. Several popular frameworks already support ONNX, such as [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/), [CustomVision.ai](https://customvision.ai/), Pytorch and Caffe2. For a complete and updated list of frameworks please visit the [Official ONNX website](https://onnx.ai/supported-tools).

[Train AI model](train-ai-model.md)

[Convert models to ONNX](conversion-samples.md)
