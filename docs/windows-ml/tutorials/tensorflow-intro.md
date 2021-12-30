---
title: TensorFlow + DirectML with Windows ML
description: Learn how to configure TensorFlow on your Windows machine
ms.date: 5/8/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials, pytorch
---

# TensorFlow + DirectML with Windows ML: Real-time object detection from video 

![Image classification flow](../../images/tutorials/tf-header.png)

This tutorial shows how to locally train and evaluate a real-time object detection model in a UWP application. The model will be trained with TensorFlow locally on your machine through the DirectML APIs, which provides GPU accelerated training across all Windows devices. The trained model will then be integrated into a UWP app which uses your webcam to detect objects in the frame in real-time, locally using Windows ML APIs.

We'll start by enabling TensorFlow on your machine.

If you'd like to learn how to train your model with TensorFlow, you can proceed to [Train a Model](tensorflow-train-model.md).

If you have a TensorFlow model, but want to learn how to convert it to an ONNX format suitable for use with WinML APIs, see [convert your model](tensorflow-convert-model.md).

If you have a model and want to learn how to create a WinML app from scratch, navigate to [Deploy your model](tensorflow-deploy-model.md). 

## Enable GPU acceleration for TensorFlow with DirectML

To enable TensorFlow on your machine, proceed through the following steps.

### Check your version of Windows

The TensorFlow with DirectML package on native Windows works on Windows 10 Version 1709 (Build 16299) or later versions of Windows. You can check your build version number by running `winver` via the **Run** command (`Windows logo key + R`).

### Check for GPU driver updates

Ensure you have the latest GPU driver installed. Select **Check for updates** in the **Windows Update** section of the **Settings** app.

### Set up the TensorFlow with DirectML preview

For use with TensorFlow, e recommend setting up a virtual Python environment inside Windows. There are many tools you can use to setup a virtual Python environment — for these instructions, we'll use [Anaconda’s miniconda](https://docs.conda.io/en/latest/miniconda.html). The rest of this setup assumes you use a miniconda environment.

### Set up Python environment

> [!NOTE]
> In the commands below, we use Python 3.6. However, the `tensorflow-directml` package works in a Python 3.5, 3.6 or 3.7 environment.

Download and install the [Miniconda Windows installer](https://docs.conda.io/en/latest/miniconda.html#windows-installers) on your machine. If you need it, there is [additional guidance for setup](https://conda.io/projects/conda/en/latest/user-guide/install/windows.html) on Anaconda’s site. Once Miniconda is installed, create an environment using Python named directml and activate it through the following commands:

`conda create --name directml python=3.6` 
`conda activate directml`

### Install the Tensorflow with DirectML package

> [!NOTE]
> The `tensorflow-directml` package only supports TensorFlow 1.15.

Install the TensorFlow with DirectML package through pip by running the following command:

`pip install tensorflow-directml`

### Verify package installation

Once you’ve installed the `tensorflow-directml` package, you can verify that it runs correctly by adding two tensors. Copy the following lines into an interactive Python session:

```py
import tensorflow.compat.v1 as tf 

tf.enable_eager_execution(tf.ConfigProto(log_device_placement=True)) 

print(tf.add([1.0, 2.0], [3.0, 4.0])) 
```

You should see output similar to the following, with the add operator placed on the DML device.
 
## Next Steps

Now that you've gotten your prerequisites sorted out, you can proceed to creation of your WinML model. [In the next part](tensorflow-train-model.md), you'll use TensorFlow to create your real-time object-detection model.

> [!IMPORTANT]
> TensorFlow, the TensorFlow logo and any related marks are trademarks of Google Inc.
