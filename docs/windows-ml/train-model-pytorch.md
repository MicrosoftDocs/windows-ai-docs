---
title: Train a model with PyTorch and export to ONNX
description: Learn how to train an ONNX model with PyTorch. Download the model as an ONNX file and run it locally with Windows Machine Learning.
ms.date: 7/10/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, pytorch
ms.localizationpriority: medium
---

# Train a model with PyTorch and export to ONNX

With the [PyTorch](https://pytorch.org/) framework and [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning-service/), you can train a model in the cloud and download it as an ONNX file to run locally with Windows Machine Learning.

## Train the model

With Azure ML, you can train a PyTorch model in the cloud, getting the benefits of rapid scale-out, deployment, and more. See [Train and register PyTorch models at scale with Azure Machine Learning](/azure/machine-learning/service/how-to-train-pytorch) for more information.

## Export to ONNX

Once you've trained the model, you can export it as an ONNX file so you can run it locally with Windows ML. See [Export PyTorch models for Windows ML](https://github.com/onnx/tutorials/blob/master/tutorials/ExportModelFromPyTorchForWinML.md) for instructions on how to natively export from PyTorch.


## Integrate with Windows ML

After you've exported the model to ONNX, you're ready to integrate it into a Windows ML application. Windows ML is available in several different programming languages, so check out a tutorial in the language you're most comfortable with.

* **C#:** [Create a Windows Machine Learning UWP application (C#)](./get-started-uwp.md)

* **Python:** [Create a Windows Machine Learning application with Python](https://github.com/Microsoft/xlang/tree/master/samples/python/winml_tutorial)

* **C++:** [Create a Windows Machine Learning Desktop application (C++)](./get-started-desktop.md)

[!INCLUDE [help](../includes/get-help.md)]
