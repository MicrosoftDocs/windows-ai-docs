---
title: How to train a model for Windows ML in Visual Studio
description: Learn how to train a model for Windows ML using Visual Studio Tools for AI with this step-by-step tutorial.
ms.date: 4/2/2019
ms.topic: article
keywords: windows 10, uwp, Windows machine learning, visual studio
---

# Train a model with CNTK

In this tutorial, we'll use [Visual Studio Tools for AI](https://aka.ms/vstoolsforai), a development extension for building, testing, and deploying Deep Learning & AI solutions, to train a model. <!--for the MNIST sample app in [Get Started (UWP)](get-started-uwp.md)-->

We'll train the model with the [Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/) framework and the [MNIST dataset](http://yann.lecun.com/exdb/mnist/), which has a training set of 60,000 examples and a test set of 10,000 examples of handwritten digits. We'll then save the model using the [Open Neural Network Exchange (ONNX)](https://onnx.ai/) format to use with Windows ML.

## Prerequisites
### Install Visual Studio Tools for AI
To get started, you'll need to download and install [Visual Studio](https://www.visualstudio.com/downloads/). Once you have Visual Studio open, activate the **Visual Studio Tools for AI** extension:

1. Click on the menu bar in Visual Studio and select "Extensions and Updates..."
2. Click on "Online" tab and select "Search Visual Studio Marketplace."
3. Search for "Visual Studio Tools for AI." 
3. Click on the **Download** button. 
4. After installation, restart Visual Studio. 

The extension will be active once Visual Studio restarts. If you're having trouble, check out [Finding Visual Studio extensions](/visualstudio/ide/finding-and-using-visual-studio-extensions).

### Download sample code
Download the [Samples for AI](https://github.com/Microsoft/samples-for-ai) repo on GitHub. The samples cover getting started with deep learning across TensorFlow, CNTK, Theano and more.

### Install CNTK
Install [CNTK for Python on Windows](/cognitive-toolkit/setup-windows-python?tabs=cntkpy24). Note that you'll also have to install Python if you haven't already.

Alternatively, to prepare your machine for deep learning model development, see [Preparing your development environment](https://github.com/Microsoft/samples-for-ai/blob/master/README.md) for a simplified installer for installing Python, CNTK, TensorFlow, NVIDIA GPU drivers (optional) and more.

## 1. Open project

Launch Visual Studio and select **File > Open > Project/Solution**. From the Samples for AI repository, select the **examples\cntk\python** folder, and open the **CNTKPythonExamples.sln** file.

![Screenshot that shows selecting the project in Visual Studio.](../images/open-solution.png)

## 2. Train the model

To set the MNIST project as the startup project, right-click on the python project and select **Set as Startup Project**.

![Open solution](../images/mnist-startup.png)

Next, open the train_mnist_onnx.py file and **Run** the project by pressing **F5** or the green **Run** button.

## 3. View the model and add it to your app

Now, the trained **mnist.onnx** model file should be in the samples-for-ai/examples/cntk/python/MNIST folder. <!--You can use this trained **mnist.onnx** model file to build the MNIST sample app in [Get Started (UWP)](get-started-uwp.md)!-->

## 4. Learn more
To learn how to speed up training deep learning models by using [Azure GPU Virtual Machines](/visualstudio/ai/tensorflow-vm) and more, visit [Artificial Intelligence at Microsoft](https://www.microsoft.com/ai) and [Microsoft Machine Learning Technologies](/azure/machine-learning/#other-microsoft-machine-learning-technologies).

[!INCLUDE [help](../includes/get-help.md)]
