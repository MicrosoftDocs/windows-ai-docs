---
title: DirectML Plugin for TensorFlow 2
description: Enable DirectML for TensorFlow 2.9
ms.author: jdykhuizen
ms.topic: article
ms.date: 06/17/2020
---

# Enable GPU Acceleration for TensorFlow 2 with tensorflow-directml-plugin

This release provides students, beginners, and professionals a way to run machine learning (ML) training on their existing DX12 enabled hardware by using the DirectML Plugin for TensorFlow. 

Learn how to configure your device to run train models with the GPU using `tensorflow-directml-plugin`.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## STEP 1: Configure your Windows Environment

The **TensorFlow-DirectML-Plugin** is available for both native Windows and Windows Subsystem for Linux (WSL). 
* Instructions for configuring [Windows Native >](gpu-tensorflow-windows.md)
* Instructions for configuring [WSL >](gpu-tensorflow-wsl.md)
* Ensure you meet the system requirements found in our [GitHub](https://github.com/microsoft/tensorflow-directml-plugin)



## STEP 2: Set up your Environment

We recommend setting up a virtual Python environment inside Windows. There are many tools that you can use to set up a virtual Python environment&mdash;for these instructions, we'll use [Anaconda's Miniconda](https://docs.conda.io/en/latest/miniconda.html). The rest of this setup assumes that you use a Miniconda environment. [Learn more about using python environments >](https://towardsdatascience.com/virtual-environments-104c62d48c54)


### Create an environment in Miniconda

Download and install the [Miniconda Windows installer](https://docs.conda.io/en/latest/miniconda.html#windows-installers) on your system. There is [additional guidance for setup](https://conda.io/projects/conda/en/latest/user-guide/install/windows.html) on Anaconda's site. Once Miniconda is installed, create an environment using Python named **tfdml_plugin**, and activate it through the following commands.

```
conda create --name tfdml_plugin python=3.9 

conda activate tfdml_plugin 
```

> [!NOTE]
> tensorflow version >= 2.9 and python version >= 3.7 supported


## STEP 3: Install base TensorFlow
Download the base TensorFlow package. Our plug-in will work with both the `tensorflow` and `tensorflow –cpu` packages. We recommend the more light-weight cpu package when not using NVIDIA CUDA.


```
pip install tensorflow-cpu==2.9
```


## STEP 4: Install tensorflow-directml-plugin
Downloading this package will automatically route operators to your DirectML device without the need for any changes in your existing scripts.

```
pip install tensorflow-directml-plugin
```
Alternatively, the package can be built from the source. [Instructions for building `tensorflow-directml-plugin` from source >](https://github.com/microsoft/tensorflow-directml-plugin/blob/main/BUILD.md)




## TensorFlow with DirectML samples and feedback 
Check out [our samples](https://github.com/microsoft/DirectML/tree/master/TensorFlow) or utilize your exisiting model scripts. If you run into issues or have feedback on the TensorFlow with DirectML package, then please [connect with our team](https://github.com/microsoft/tensorflow-directml-plugin/issues). 