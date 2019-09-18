---
title: Windows AI
description: Get help choosing which Microsoft AI solution is right for your application.
author: mattwojo
ms.author: mattwoj
ms.date: 09/18/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, microsoft ai, compare, comparison, windows vision skills, Direct ML
ms.localizationpriority: medium
---

# Which Windows AI solution is right for me?

Microsoft offers several different AI solutions, which means you have several options at your disposal. But how do you choose which one to use for your application? Let's break it down.

## I want to integrate a machine learning model into my application and run it on the device by taking full advantage of hardware acceleration

[Windows Machine Learning](windows-ml/index.md) is the right choice for you. These high-level WinRT APIs work on Windows 10 applications (UWP, desktop) and evaluate models directly on the device. You can even choose to take advantage of the device's GPU (if it has one) for better performance.

## I want to integrate computer vision into my application and take advantage of platform optimizations

[Windows Vision Skills](windows-vision-skills/index.md) is the way to go. This simple framework allows you to build custom vision applications that leverage hardware acceleration on edge devices. You can combine pre-built libraries to accomplish common image processing tasks and ML models for specialized tasks.

## I want to have fuller control over resource utilization during model execution for high-intensive applications

[DirectML](https://docs.microsoft.com/windows/desktop/direct3d12/dml) is what you want. These DirectX-style APIs provide a programming paradigm that will feel familiar to C++ game developers, and allow you to take full advantage of the hardware.

## I want to train, test, and deploy ML models with a framework that is familiar to a .NET developer

Check out [ML.NET](https://dotnet.microsoft.com/apps/machinelearning-ai/ml-dotnet), a machine learning framework built for .NET developers.

## I want to leverage the power of the Azure cloud for training and deploying ML models

See [What are the machine learning products at Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning) for a comprehensive list of the solutions available from Microsoft, including many products and services that run on Azure.
