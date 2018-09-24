---
author: rosanevallim
title: Get ONNX models for Windows ML
description: Windows ML evaluates models in the ONNX format, allowing you to interchange models between various ML frameworks and tools.
ms.author: rovalli
ms.date: 09/24/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Get ONNX models for Windows ML

> [!NOTE]
> Windows ML is a **pre-released** product which may be substantially modified before it’s commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.
>
> To try out the pre-released Windows ML, you'll need the [Windows 10 Insider Preview](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewiso) (Build 17728 or higher) and the [Windows 10 SDK](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewSDK) (Build 17723 or higher).

Windows ML evaluates models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. ONNX is an open format for ML models, allowing you to interchange models between various [ML frameworks and tools](http://onnx.ai/supported-tools).

> [!IMPORTANT]
> Windows ML requires ONNX models, [version 1.2](https://github.com/onnx/onnx/tree/rel-1.2.2) or higher.

To get an ONNX model to use with Windows ML, you can:

- Download a pre-trained ONNX model from the [ONNX Model Zoo](https://github.com/onnx/models).
- Train your own model with services like [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier), [Azure Machine Learning](https://azure.microsoft.com/overview/machine-learning/), or [VS Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vs/), and export to ONNX format.
- Convert models trained in other ML frameworks into ONNX format with [WinMLTools](convert-model-winmltools.md) converters or the [ONNX tutorials](https://github.com/onnx/tutorials).

Once you have an ONNX model, you'll [integrate the model](integrate-model.md) into your app's code, and then, you'll be able use machine learning in your Windows apps and devices!

[!INCLUDE [help](includes/get-help.md)]