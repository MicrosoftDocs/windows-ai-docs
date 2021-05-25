---
title: What is a machine learning model?
description: Learn about what a model is in the context of Windows Machine Learning.
ms.date: 5/24/2021
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# What is a machine learning model?

A machine learning model is a file that has been trained to recognize certain types of patterns. You train a model over a set of data, providing it an algorithm that it can use to reason over and learn from those data.

Once you have trained the model, you can use it to reason over data that it hasn't seen before, and make predictions about those data. For example, let's say you want to build an application that can recognize a user's emotions based on their facial expressions. You can train a model by providing it with images of faces that are each tagged with a certain emotion, and then you can use that model in an application that can recognize any user's emotion. See the [Emoji8 sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/Emoji8/UWP/cs) for an example of such an application.

![Windows ML model flow graphic](../images/winml-model-flow.png)

## When to use Machine Learning

Good machine learning scenarios often have the following common properties: 
1.	They involve a repeated decision or evaluation which you want to automate and need consistent results.
2.	It is difficult or impossible to explicitly describe the solution or criteria behind a decision.
3.	You have labeled data, or existing examples where you can describe the situation and map it to the correct result.

Windows Machine Learning uses the [Open Neural Network Exchange (ONNX)](https://onnx.ai/) format for its models. You can download a pre-trained model, or you can train your own model. See [Get ONNX models for Windows ML](get-onnx-model.md) for more information.

## Get started

You can get started with Windows Machine Learning by following [one of our full-app tutorials](tutorials/index.md) or jumping straight to the [Windows Machine Learning samples](samples.md).

[!INCLUDE [help](../includes/get-help.md)]
