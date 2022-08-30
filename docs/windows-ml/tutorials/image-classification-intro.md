---
title: Image classification with Custom Vision and Windows ML
description: Learn the prerequisites for creating your own Windows ML model and image classification app.
ms.date: 08/30/2022
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials
ms.custom: RS5
---

# Image classification with Custom Vision and Windows Machine Learning

![Image classification flow](../../images/tutorials/image-classification-flow.png)

This guide shows you how to: train a neural network model to classify images of food using Azure Custom Vision service; export the model to ONNX format; and deploy the model in a Windows Machine Learning (Windows ML) application running locally on a Windows device. You don't need any previous expertise in machine learning! We'll guide you step by step through the process. 

If you'd like to learn how to build and train a model with Custom Vision, then you can proceed to [Train a model](image-classification-train-model.md).

If you have a model and you want to learn how to create a Windows ML app from scratch, then see the complete [Windows ML app tutorial](image-classification-deploy-model.md). 

If you want to start with a pre-existing Visual Studio project for a Windows ML app, then you can clone the [Custom Vision and Windows ML](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/Tutorial%20Samples/Custom%20Vision%20and%20Windows%20ML) tutorial sample app, and use that as your starting point.

## Scenario

In this tutorial, we'll create a machine learning food classification application that runs on Windows devices. The model will be trained to recognize certain types of patterns to classify an image of food, and when given an image it will return a classification tag and the associated percentage confidence value of that classification.

### Prerequisites for model training

To build and train a model, you'll need a subscription to Azure Custom Vision services.

If you're new to Azure, then you can sign up for an [Azure free account](https://azure.microsoft.com/free/services/machine-learning/). That will give you an opportunity to build, train, and deploy machine learning models with Azure AI.

> [!TIP]
> Are you interested in learning more about Azure sign-up options and Azure free accounts? Then check out [Create an Azure account](/learn/modules/create-an-azure-account/).

### Prerequisites for Windows ML app deployment

To create and deploy a Windows ML app, you'll need the following:

* Windows 10, version 1809 (build 17763) or later. You can check your build version number by running `winver` via the **Run** command (Windows logo key + R).
* Windows SDK for build 17763 or later. To download, see [Windows SDK](https://developer.microsoft.com/windows/downloads/windows-sdk/).
* Visual Studio 2017 version 15.7 or later; but we recommend that you use Visual Studio 2022 or later. Some screenshots in this tutorial might differ from the UI you'll see. To download Visual Studio, see [Downloads and tools for Windows development](https://developer.microsoft.com/windows/downloads/).
* Windows ML Code Generator (`mlgen`) Visual Studio extension. Download it for [Visual Studio 2019 or later](https://marketplace.visualstudio.com/items?itemName=WinML.mlgenv2) or for [Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=WinML.mlgen).
* If you decide to create a Universal Windows Platform (UWP) app, then you'll need to enable the Universal Windows Platform development workload in Visual Studio.
* Enable Developer mode on your PC&mdash;see [Enable your device for development](/windows/apps/get-started/enable-your-device-for-development).

> [!NOTE]
> Windows ML APIs are built in to the latest versions of Windows 10 (1809 or higher) and Windows Server 2019. If your target platform is an older version of Windows, then you can [port your Windows ML app to the redistributable NuGet package (Windows 8.1 or higher)](../port-app-to-nuget.md). 

## Prepare the data

Machine learning models must be trained with existing data. In this guide, you'll use a dataset of food images from Kaggle Open Datasets. That dataset is distributed under the public domain license.

> [!IMPORTANT]
> To use this dataset, you need to adhere to the term of using the Kaggle site, and the license terms accompanying the `Food-11` dataset itself. Microsoft makes no warranty or representation concerning the site or this dataset.

The dataset has three splits&mdash;evaluation, training, and validation&mdash;and contains 16,643 food images grouped into 11 major food categories. The images in the dataset of each category of food are placed in a separate folder, which makes the model training process more convenient.

Download the dataset from [Food-11 image dataset](https://www.kaggle.com/trolukovich/food11-image-dataset). The dataset is around 1GB in size, and you might be asked to create an account on the Kaggle website to download the data.

![Food image datasaet](../../images/tutorials/food-image-dataset.png)

If you prefer, you're welcome to use any other dataset of relevant images. As a minimum, we recommend that you use at least 30 images per tag in the initial training set. You'll also want to collect a few extra images to test your model once it's trained.

Additionally, make sure that all of your training images meet the following criteria:

*	`.jpg`, `.png`, `.bmp`, or `.gif` format.
*	No greater than 6MB in size (4MB for prediction images).
*	No less than 256 pixels on the shortest edge; any images shorter than this will be automatically scaled up by the Custom Vision Service.

## Next steps

Now that you've gotten your prerequisites sorted out, and have prepared your dataset, you can proceed to the creation of your Windows ML model. In the next part ([Train your model with Custom Vision](image-classification-train-model.md)), you'll use the web-based Custom Vision interface to create and train your classification model.
