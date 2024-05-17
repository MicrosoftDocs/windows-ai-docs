---
title: GPU acceleration in WSL
description: Direct Machine Learning (DirectML) powers GPU-accelleration in Windows Subsystem for Linux
ms.topic: article
ms.date: 05/22/2024
---

# GPU accelerated ML training

![Windows ML graphic](../images/winml-graphic.png)

This documentation covers setting up GPU accelerated machine learning (ML) training scenarios for the [Windows Subsystem for Linux](/windows/wsl/about) (WSL) and native Windows.

This functionality supports both professional and beginner scenarios. Below you'll find pointers to step-by-step guides on how to get your system set up depending on your level of expertise in ML, your GPU vendor, and the software library that you intend to use.

## NVIDIA CUDA in WSL

If you're a professional data scientist who uses a native Linux environment day-to-day for inner-loop ML development and experimentation, and you have an NVIDIA GPU, then we recommend setting up [NVIDIA CUDA in WSL](gpu-cuda-in-wsl.md).

## PyTorch with DirectML 

To use PyTorch with a framework that works across the breadth of DirectX 12 capable GPUs, we recommend setting up the **PyTorch with DirectML** package. This package accelerates workflows on AMD, Intel, and NVIDIA GPUs. 

If you're more familiar with a native Linux environment, then we recommend running [PyTorch with DirectML inside WSL](pytorch-wsl.md). 

If you're more familiar with Windows, then we recommend running [PyTorch with DirectML on native Windows](pytorch-windows.md). 

## TensorFlow with DirectML 

> [!IMPORTANT]
> This project is now discontinued, and isn't actively being worked on.

To use TensorFlow with a framework that works across the breadth of DirectX 12 capable GPUs, we recommend setting up the [**TensorFlow with DirectML** package](gpu-tensorflow-plugin.md). This package accelerates workflows on AMD, Intel, and NVIDIA GPUs.

## Next steps

* [Enable NVIDIA CUDA in WSL](gpu-cuda-in-wsl.md)
* [Enable TensorFlow with DirectML](gpu-tensorflow-plugin.md)
* [Enable PyTorch with DirectML inside WSL](pytorch-wsl.md)
* [Enable PyTorch with DirectML on native Windows](pytorch-windows.md)
