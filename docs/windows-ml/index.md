---
author: rosanevallim
title: Introduction to Windows Machine Learning
description: With Windows ML, you can use trained machine learning models in your Windows applications.
ms.author: rovalli
ms.date: 5/29/2020
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows Machine Learning

Implement Machine Learning in your Windows apps using Windows ML — a high-performance, reliable API for deploying hardware-accelerated ML inferences on Windows devices. 

![Windows ML graphic](../images/winml-graphic.png)

## Overview

Windows ML is built into the latest versions of Windows 10 and Windows Server 2019, and is also available as a [NuGet package](https://aka.ms/windowsmlredist) for down-level reach to Windows 8.1. Windows ML provides developers with the following advantages:

- **Ease of development:** With Windows ML built into the latest versions of Windows 10 and Windows Server 2019, all you need is Visual Studio and a trained ONNX model, which can be distributed along with the Windows application. Also, if you need to deliver your AI-based features to older versions of Windows (down to 8.1), Windows ML is also available as a NuGet package that you can distribute with your application.

- **Broad hardware support:** Windows ML allows you to write your ML workload once and automatically get highly optimized performance across different hardware vendors and silicon types, such as CPUs, GPUs, and AI accelerators. In addition, Windows ML guarantees consistent behavior across the range of supported hardware.

- **Low latency, real-time results:** ML models can be evaluated using the processing capabilities of the Windows device, enabling local, real-time analysis of large data volumes, such as images and video. Results are available quickly and efficiently for use in performance-intensive workloads like game engines, or background tasks such as indexing for search.

- **Increased flexibility:** The option to evaluate ML models locally on Windows devices lets you address a broader range of scenarios. For example, evaluation of ML models can run while the device is offline, or when faced with intermittent connectivity. This also lets you address scenarios where not all data can be sent to the cloud due to privacy or data sovereignty issues.

- **Reduced operational costs:** Training ML models in the cloud and then evaluating them locally on Windows devices can deliver significant savings in bandwidth costs, with only minimal data sent to the cloud—as might be needed for continual improvement of your ML model. Moreover, when deploying the ML model in a server scenario, developers can leverage Windows ML hardware acceleration to speed-up model serving, reducing the number of machines needed in order to handle the workload.

## Machine Learning models

A machine learning model is a file that has been trained to recognize certain types of patterns. You train a model over a set of data, providing it an algorithm that it can use to reason over and learn from those data.

Once you have trained the model, you can use it to reason over data that it hasn't seen before, and make predictions about those data. For example, let's say you want to build an application that can recognize a user's emotions based on their facial expressions. You can train a model by providing it with images of faces that are each tagged with a certain emotion, and then you can use that model in an application that can recognize any user's emotion. See the [Emoji8 sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/Emoji8/UWP/cs) for an example of such an application, or check out [What is a machine learning model](what-is-a-machine-learning-model.md) to learn more.

Windows Machine Learning uses the [Open Neural Network Exchange (ONNX)](https://onnx.ai/) format for its models. You can download a pre-trained model, or you can train your own model. See [Get ONNX models for Windows ML](get-onnx-model.md) for more information.

## Get Started

To learn a bit more about the different ways to incorporate Windows Machine Learning into your app, check out our [get started page](get-started.md).

Looking to create your first app using Windows Machine Learning? Check out the [WinML tutorials](tutorials/index.md) for an overview of the different ways to train a model, and incorporate it into your WinML application.

## FAQ

Interested in learning more about Machine Learning solutions and your options? For a full overview of the choices available, see [Compare AI solutions](../windows-ai-comparison.md), or learn more with the [WinML FAQ](faq.md).

[!INCLUDE [help](../includes/get-help.md)]
