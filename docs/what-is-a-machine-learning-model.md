---
author: eliotcowley
title: What is a machine learning model?
description: Learn about what a model is in the context of Windows Machine Learning.
ms.author: elcowle
ms.date: 2/13/2018
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# What is a machine learning model?

A machine learning model is a file that has been trained to recognize certain types of patterns. You train a model over a set of data, providing it an algorithm that it can use to reason over and learn from those data.

Once you have trained the model, you can use it to reason over data that it hasn't seen before, and make predictions about those data. For example, let's say you want to build an application that can recognize a user's emotions based on their facial expressions. You can train a model by providing it with images of faces that are each tagged with a certain emotion, and then you can use that model in an application that can recognize any user's emotion. See the [Emoji8 sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/Emoji8/UWP/cs) for an example of such an application.

Windows Machine Learning uses the [Open Neural Network Exchange (ONNX)](https://onnx.ai/) format for its models. You can download a pre-trained model, or you can train your own model. See [Get ONNX models for Windows ML](get-onnx-model.md) for more information.

[!INCLUDE [help](includes/get-help.md)]
