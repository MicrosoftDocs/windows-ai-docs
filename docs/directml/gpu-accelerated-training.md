---
title: GPU acceleration in WSL
description: Direct Machine Learning (DirectML) powers GPU-accelleration in Windows Subsystem for Linux
ms.topic: article
ms.date: 06/17/2020
---

# GPU accelerated ML training

![Windows ML graphic](../images/winml-graphic.png)

This documentation covers what's currently supported by the GPU accelerated machine learning (ML) training for the [Windows Subsystem for Linux](/windows/wsl/about) (WSL) and native Windows.

This release supports both professional and beginner scenarios. Below you'll find pointers to step-by-step guides on how to get your system set up depending on your level of expertise in ML, your GPU vendor, and the software library that you intend to use.

## Professionals

If you're a professional data scientist who uses a native Linux environment day-to-day for inner-loop ML development and experimentation, and you have an NVIDIA GPU, then we recommend setting up the [NVIDIA CUDA preview in WSL 2](gpu-cuda-in-wsl.md).

## Students and beginners 

If you're a student or beginner looking to start building your knowledge in the ML space, then we recommend setting up the **TensorFlow with DirectML** back-end package. This package currently accelerates workflows on AMD and Intel GPUs. Support for NVIDIA GPUs is coming soon. 

If you're more familiar with a native Linux environment, and you're getting started with ML workflows, then we recommend running the [TensorFlow with DirectML package inside WSL 2](gpu-tensorflow-wsl.md). 

If you're more familiar with Windows, and you're getting started with ML workflows, then we recommend setting up the [TensorFlow with DirectML package on native Windows](gpu-tensorflow-windows.md). 

## Next steps

* [Enable NVIDIA CUDA preview in WSL 2](gpu-cuda-in-wsl.md)
* [Enable TensorFlow with DirectML package inside WSL 2](gpu-tensorflow-wsl.md)
* [Enable TensorFlow with DirectML package on native Windows](gpu-tensorflow-windows.md)
