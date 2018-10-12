---
author: rosanevallim
title: Frequently asked questions
description: This page contains answers to the most popular questions from the community
ms.author: rovalli
ms.date: 10/12/2018
ms.topic: article
ms.prod: windows
ms.technology: desktop
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# How do I know if the ONNX model I have will run with WindowsML?

THe minimum ONNX version supported by the Windows ML API is 1.2.2. When training a model, check if your framework supports saving models to the 1.2.2 format.
If you already have a model and wants to convert it to ONNX, we recommend installing the latest version of the winmltools package, which supports the 1.2.2 format.

# Which version of winmltools should I use?
We always recommend you download and install the latest version of the winmltools package. THis will ensure you can create ONNX models that target the latest versions of Windows.

# Which version of Visual Studio should I use in order to get automatic code generation (mlgen)?
THe minimum recommended version of Visual Studio with support for mlgen is 15.8.7.

# Can I use onnxmltools instead of winmltools?
Yes, you can, but you will need to make sure you install the correct version of onnxmltools in order to target ONNX v1.2.2, which is the minimum
ONNX version supported by Windows ML. If you are unsure of which version to install, we recommend installing the latest version of winmltools instead. This 
will ensure you will be able to target the ONNX version supported by Windows.


