---
title: Get started with ONNX models in your WinUI app with ONNX Runtime
description: TODO Description needed.
ms.author: drewbat
author: drewbatgit
ms.date: 05/21/2024
ms.topic: article
#customer intent: As a <role>, I want <what> so that <why>.
---

# Get started with ONNX models in your WinUI app with ONNX Runtime



## What is the ONNX runtime

[TBD - this summary is just the intro paragraph to the ONNX main page. Do we have anything else interesting to say here?]

ONNX Runtime is a cross-platform machine-learning model accelerator, with a flexible interface to integrate hardware-specific libraries. ONNX Runtime can be used with models from PyTorch, Tensorflow/Keras, TFLite, scikit-learn, and other frameworks. For more information, see the ONNX Runtime website at [https://onnxruntime.ai/docs/](https://onnxruntime.ai/docs/).

## Prerequisites

- Your device must have developer mode enabled. For more information see [Enable your device for development](/windows/apps/get-started/enable-your-device-for-development).
- Visual Studio 2022 or later with the [TBD - required workloads / components] workload.

## Create a new C# WinUI app

In Visual Studio, create a new project. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "winui", then select the **Black app, Packaged (WinUI3 in Desktop)** template. Name the new project "TBD_DescriptiveSampleName".

## Add references to Nuget packages

In **Solution Explorer**, right-click **Dependencies** and select **Manage NuGet packages...**. In the NuGet package manager, select the **Browse** tab and search for "Microsoft.WindowsAppSDK". Select the latest stable version in the **Version** drop-down and then click **Install**.

