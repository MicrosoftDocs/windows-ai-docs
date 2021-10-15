---
title: GPU acceleration in WSL
description: Direct Machine Learning (DirectML) powers GPU-accelleration in Windows Subsystem for Linux
ms.topic: article
ms.date: 06/17/2020
---

# GPU accelerated ML training

![Windows ML graphic](../images/winml-graphic.png)

This documentation covers setting up GPU accelerated machine learning (ML) training sceanrios for the [Windows Subsystem for Linux](/windows/wsl/about) (WSL) and native Windows.

This functionality supports both professional and beginner scenarios. Below you'll find pointers to step-by-step guides on how to get your system set up depending on your level of expertise in ML, your GPU vendor, and the software library that you intend to use.

## NVIDIA CUDA in WSL

If you're a professional data scientist who uses a native Linux environment day-to-day for inner-loop ML development and experimentation, and you have an NVIDIA GPU, then we recommend setting up [NVIDIA CUDA in WSL](gpu-cuda-in-wsl.md).

## TensorFlow with DirectML 

If you're a student, beginner, or professional looking for a framework that works across the breadth of DirectX 12 capable GPUs, then we recommend setting up the **TensorFlow with DirectML** package. This package accelerates workflows on AMD, Intel, and NVIDIA GPUs. 

If you're more familiar with a native Linux environment, then we recommend running [TensorFlow with DirectML inside WSL](gpu-tensorflow-wsl.md). 

If you're more familiar with Windows, then we recommend running [TensorFlow with DirectML on native Windows](gpu-tensorflow-windows.md). 

## Next steps

* [Enable NVIDIA CUDA in WSL](gpu-cuda-in-wsl.md)
* [Enable TensorFlow with DirectML inside WSL](gpu-tensorflow-wsl.md)
* [Enable TensorFlow with DirectML on native Windows](gpu-tensorflow-windows.md)
