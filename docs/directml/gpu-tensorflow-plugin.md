---
title: DirectML Plugin for TensorFlow 2
description: Enable DirectML for TensorFlow 2.9
ms.author: jdykhuizen
ms.topic: article
ms.date: 06/17/2020
---

# Enable GPU Acceleration for TensorFlow 2 with tensorflow-directml-plugin

This release provides students, beginners, and professionals a way to run machine learning (ML) training on their existing DX12 enabled hardware by using the DirectML Plugin for TensorFlow 2. 

Learn how to configure your device to run and train models with the GPU using `tensorflow-directml-plugin`.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## STEP 1: Minimum System Requirements
Before installing the TensorFlow-DirectML-Plugin, ensure your version of Windows or WSL supports TensorFlow-DirectML-Plugin.

### Windows Native

* Windows 10 Version 1709, 64-bit (Build 16299 or higher) or Windows 11 Version 21H2, 64-bit (Build 22000 or higher)
* Python x86-64 3.7, 3.8, 3.9 or 3.10
* One of the following supported GPUs:
  * AMD Radeon R5/R7/R9 2xx series or newer
  * Intel HD Graphics 5xx or newer
  * NVIDIA GeForce GTX 9xx series GPU or newer

### Windows Subsystem for Linux

* Windows 10 Version 21H2, 64-bit (Build 20150 or higher) or Windows 11 Version 21H2, 64-bit (Build 22000 or higher)
* Python x86-64 3.7, 3.8, 3.9 or 3.10
* One of the following supported GPUs:
  * AMD Radeon R5/R7/R9 2xx series or newer, and [20.20.01.05 driver or newer](https://www.amd.com/en/support)
  * Intel HD Graphics 6xx or newer, and [28.20.100.8322 driver or newer](https://www.intel.com/content/www/us/en/download/19344/intel-graphics-windows-dch-drivers.html)
  * NVIDIA GeForce GTX 9xx series GPU or newer, and [460.20 driver or newer](https://www.nvidia.com/download/index.aspx)

## Install the latest GPU Driver
Ensure that you have the latest GPU driver installed for your hardware. Select **Check for updates** in the **Windows Update** section of the **Settings** app. If needed, pick up an install from your hardware vendor using the above links.


## STEP 2: Configure your Windows Environment

### Windows Native
The **TensorFlow-DirectML-Plugin** package on native Windows works starting with Windows 10, version 1709 (Build 16299 or higher). You can check your build version number by running `winver` via the **Run** command (Windows logo key + R).

### Windows Subsystem for Linux
Once you've installed the above driver, ensure you [enable WSL](/windows/wsl/install-win10) and [install a glibc-based distribution](/windows/wsl/install-win10#install-your-linux-distribution-of-choice) (like Ubuntu or Debian). For our testing, we used Ubuntu. Ensure you have the latest kernel by selecting **Check for updates** in the **Windows Update** section of the Settings app. 

> [!NOTE]
> Ensure you have the **Receive updates for other Microsoft products when you update Windows** enabled. You can find it in **Advanced options** within the **Windows Update** section of the Settings app. 

For these features, you need a kernel version of 5.10.43.3 or higher. You can check the version number by running the following command in PowerShell. 

```
wsl cat /proc/version
```

## STEP 3: Set up your Environment

We recommend setting up a virtual Python environment inside Windows. There are many tools that you can use to set up a virtual Python environment&mdash;for these instructions, we'll use [Anaconda's Miniconda](https://docs.conda.io/en/latest/miniconda.html). The rest of this setup assumes that you use a Miniconda environment. [Learn more about using python environments](https://towardsdatascience.com/virtual-environments-104c62d48c54)


### Create an environment in Miniconda

Download and install the [Miniconda Windows installer](https://docs.conda.io/en/latest/miniconda.html#windows-installers) on your system. There is [additional guidance for setup](https://conda.io/projects/conda/en/latest/user-guide/install/windows.html) on Anaconda's site. Once Miniconda is installed, create an environment using Python named **tfdml_plugin**, and activate it through the following commands.

```
conda create --name tfdml_plugin python=3.9 

conda activate tfdml_plugin 
```

> [!NOTE]
> tensorflow version >= 2.9 and python version >= 3.7 supported


## STEP 4: Install base TensorFlow
Download the base TensorFlow package. Our plugin will work with both the `tensorflow` and `tensorflow –cpu` packages. If you're looking for the smallest install footprint, we recommend you utilize the CPU only package.


```
pip install tensorflow-cpu==2.9
```


## STEP 5: Install tensorflow-directml-plugin
Installing this package automatically enables the DirectML backend for existing scripts without any code changes.

```
pip install tensorflow-directml-plugin
```
> [!NOTE]
> If your training scripts hardcodes the device string to something other than `"GPU"` may throw errors.


Alternatively, the package can be built from the source. [Instructions for building `tensorflow-directml-plugin` from source](https://github.com/microsoft/tensorflow-directml-plugin/blob/main/BUILD.md)




## TensorFlow with DirectML samples and feedback 
Check out [our samples](https://github.com/microsoft/DirectML/tree/master/TensorFlow) or utilize your exisiting model scripts. If you run into issues or have feedback on the TensorFlow-DirectML-Plugin package, then please [connect with our team](https://github.com/microsoft/tensorflow-directml-plugin/issues). 