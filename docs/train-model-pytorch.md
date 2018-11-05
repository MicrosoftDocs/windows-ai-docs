---
author: eliotcowley
title: Train a model with PyTorch
description: Learn how to train an ONNX model with PyTorch.
ms.author: elcowle
ms.date: 11/5/2018
ms.topic: article
ms.prod: windows
ms.technology: desktop
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, pytorch
ms.localizationpriority: medium
---

# Train a model with PyTorch

With the [PyTorch](https://pytorch.org/) framework, you can train a model locally or in the cloud, and then download the model as an ONNX file to run locally with Windows Machine Learning.

## Train locally

See [mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/mnist.py) in the Azure/MachineLearningNotebooks repository for an example of how to train an MNIST model locally using PyTorch.

To learn more about PyTorch, see the PyTorch [tutorials](https://pytorch.org/tutorials/) and [documentation](https://pytorch.org/docs/stable/index.html).

## Train in the cloud

With Azure Machine Learning and PyTorch, you can train a model in the cloud and then download it to run locally with Windows Machine Learning. The [Azure/MachineLearningNotebooks repository on GitHub](https://github.com/Azure/MachineLearningNotebooks) has several samples and tutorials for Azure ML, most of which are [Jupyter notebooks](https://jupyter.org/). See the [README](https://github.com/Azure/MachineLearningNotebooks/blob/master/README.md) for information on how to run these Azure ML tutorials with Azure Notebooks or your own Jupyter Notebook server.

Once you've gotten everything set up, you can open the [MNIST Handwritten Digit Classification using ONNX and Azure ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb) notebook and follow the tutorial to learn how to train an MNIST model using PyTorch and Azure ML. (You can skip the **Deploying as a web service** section if you're using WinML to run the model.)

[!INCLUDE [help](includes/get-help.md)]
