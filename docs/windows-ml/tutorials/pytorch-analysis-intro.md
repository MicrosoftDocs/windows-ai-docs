---
title: Data analysis with PyTorch and Windows ML
description: Learn the steps to create a ML data analysis model using PyTorch, export it to ONNX, and deploy it in a local app
ms.date: 8/17/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials, pytorch
---

# Data analysis with PyTorch and Windows ML

![Header image for PyTorch](../../images/tutorials/pytorch/pytorch-header.png)

Windows Machine Learning can be used to run predictions on tabular datasets, predicting numeric values based on independent input variables. This guide uses a specific dataset in Excel format, but the procedures outlined will work for any related task using a tabular dataset of your choice.

This guide will show you how to solve a classification task with a neural network using the PyTorch library, export the model to the ONNX format, and deploy it in a Windows Machine Learning application running locally on your Windows device.  

Basic knowledge in Python and C# programming languages is required. Previous experience in machine learning is preferable but not required.

If you'd like to move straight on to installation, see [Install PyTorch](pytorch-analysis-installation.md).

If you've set up PyTorch already, start the model training process by [getting the data](pytorch-analysis-data.md).

Once you're ready to go with the data, you can start to [train your model](pytorch-analysis-train-model.md), and then [convert it to the ONNX format](pytorch-analysis-convert-model.md).

If you have an ONNX model and want to learn how to create a WinML app from scratch, navigate to [deploy your model](pytorch-analysis-deploy-model.md).

> [!NOTE]
> If you want, you can clone the Windows Machine Learning samples repo and run the completed code for this tutorial. You can find [the PyTorch training solution here](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/Tutorial%20Samples/PyTorch%20Data%20Analysis/PyTorch%20Training%20-%20Data%20Analysis), or [the completed Windows ML app here](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/Tutorial%20Samples/PyTorch%20Data%20Analysis/Windows%20ML%20code%20-%20Data%20Analysis). If you're using the PyTorch file, make sure you set up the relevant PyTorch interpreter before you run it.

## Scenario

In this tutorial, we'll create a machine learning data analysis application to predict the type of iris flowers. For this purpose, you will use Fisher’s iris flower dataset. The model will be trained to recognize certain types of iris patterns and predict the correct type.

## Prerequisites for PyTorch - model training:

PyTorch is supported on the following Windows distributions: 

* Windows 7 and greater. Windows 10 or greater recommended. 
* Windows Server 2008 r2 and greater 

To use Pytorch on Windows, you must have Python 3.x installed. Python 2.x is not supported. 

## Prerequisites for Windows ML app deployment

To create and deploy a WinML app, you'll need the following: 

*	Windows 10 version 1809 (build 17763) or higher. You can check your build version number by running `winver` via the Run command `(Windows logo key + R)`.
*	Windows SDK for build 17763 or higher. [You can get the SDK here.](https://developer.microsoft.com/windows/downloads/windows-10-sdk/)
*	Visual Studio 2017 version 15.7 or later. We recommend using Visual Studio 2019, and some screenshots in this tutorial may be different if you use VS2017 instead. [You can get Visual Studio here.](https://developer.microsoft.com/windows/downloads/)
*	You'll also need to [enable Developer Mode on your PC](/windows/apps/get-started/enable-your-device-for-development)

> [!NOTE]
> Windows ML APIs are built into the latest versions of Windows 10 (1809 or higher) and Windows Server 2019. If your target platform is older versions of Windows, you can [port your WinML app to the redistributable NuGet package (Windows 8.1 or higher)](../port-app-to-nuget.md). 

## Next Steps

We'll start by [installing PyTorch and configuring our environment](pytorch-analysis-installation.md)

> [!IMPORTANT]
> PyTorch, the PyTorch logo and any related marks are trademarks of Facebook, Inc.
