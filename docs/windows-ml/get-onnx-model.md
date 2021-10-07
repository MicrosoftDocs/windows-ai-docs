---
author: rosanevallim
title: ONNX models
description: Windows ML evaluates models in the ONNX format, allowing you to interchange models between various ML frameworks and tools.
ms.author: rovalli
ms.date: 6/5/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, onnx
ms.localizationpriority: medium
---

# ONNX models

Windows Machine Learning supports models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai/) format. ONNX is an open format for ML models, allowing you to interchange models between various [ML frameworks and tools](https://onnx.ai/supported-tools).

There are several ways in which you can obtain a model in the ONNX format, including:

- [ONNX Model Zoo](https://github.com/onnx/models): Contains several pre-trained ONNX models for different types of tasks. Download a version that is supported by Windows ML and you are good to go!

- [Native export from ML training frameworks](https://onnx.ai/supported-tools): Several training frameworks support native export functionality to ONNX, like Chainer, Caffee2, and PyTorch, allowing you to save your trained model to specific versions of the ONNX format. In addition, services such as [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning-service/) and [Azure Custom Vision](/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) also provide native ONNX export.
    - To learn how to train and export an ONNX model in the cloud using Custom Vision, check out [Tutorial: Use an ONNX model from Custom Vision with Windows ML (preview)](/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Convert existing models using ONNXMLTools](./onnxmltools.md): This Python package allows models to be converted from several training framework formats to ONNX. As a developer, you can specify which version of ONNX you would like to convert your model to, depending on which builds of Windows your application targets. If you are not familiar with Python, you can use [Windows ML's UI-based Dashboard](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) to easily convert your models with just a few clicks.

> [!IMPORTANT]
> Not all ONNX versions are supported by Windows ML. In order to know which ONNX versions are officially supported in the Windows versions targeted by your application, please check [ONNX versions and Windows builds](onnx-versions.md).

Once you have an ONNX model, you'll [integrate the model](./integrate-model.md) into your app's code, and then you'll be able use machine learning in your Windows apps and devices!

[!INCLUDE [help](../includes/get-help.md)]
