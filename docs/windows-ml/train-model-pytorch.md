---
author: eliotcowley
title: Train a model with PyTorch and export to ONNX
description: Learn how to train an ONNX model with PyTorch.
ms.author: elcowle
ms.date: 7/5/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, pytorch
ms.localizationpriority: medium
---

# Train a model with PyTorch and export to ONNX

With the [PyTorch](https://pytorch.org/) framework and [Azure Machine Learning service](https://azure.microsoft.com/services/machine-learning-service/), you can train a model in the cloud and download it as an ONNX file to run locally with Windows Machine Learning.

## Train the model

With Azure ML, you can train a PyTorch model in the cloud, getting the benefits of rapid scale-out, deployment, and more. See [Train and register PyTorch models at scale with Azure Machine Learning service](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch) for more information.

## Export to ONNX

Once you've trained the model, you can export it as an ONNX file so you can run it locally with Windows ML. Before attempting the export, however, check [ONNX versions and Windows builds](https://docs.microsoft.com/windows/ai/windows-ml/onnx-versions) to determine which ONNX versions are supported on the Windows build you're targeting.

When you're ready to export, see [Exporting model from PyTorch to ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb).

## Integrate with Windows ML

After you've exported the model to ONNX, you're ready to integrate it into a Windows ML application. Windows ML is available in several different programming languages, so check out a tutorial in the language you're most comfortable with.

* **C#:** [Create a Windows Machine Learning UWP application (C#)](https://docs.microsoft.com/windows/ai/windows-ml/get-started-uwp)

* **Python:** [Create a Windows Machine Learning application with Python](https://github.com/Microsoft/xlang/tree/master/samples/python/winml_tutorial)

* **C++:** [Create a Windows Machine Learning Desktop application (C++)](https://docs.microsoft.com/windows/ai/windows-ml/get-started-desktop)

[!INCLUDE [help](../includes/get-help.md)]
