---
author: serenaz
title: Introduction to machine learning
description: 
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, windows machine learning
ms.localizationpriority: medium
---
# Introduction to machine learning

![Windows machine learning graphic](images/brain.png)

## What is machine learning?

Machine learning (ML) is a term coined in 1959 by Arthur Samuel and currently defines a field of computer science. By leveraging statistical techniques, machine learning gives machines the ability to learn and improve their performance on a specific task.

This learning capability is especially useful in scenarios where designing and programming explicit algorithms with good performance is difficult or infeasible. The most typical scenarios include detecting spam emails, optical character recognition, computer vision or recommendation algorithms.

## What is a model?

By combining ML algorithms and huge amounts of training data, ML algorithms can generate models that can predict the correct output when presented with a new input.

For example, consider the scenario where we want to detect spam email, where the objective is to generate a model that takes in an input in real-time (an email), and generates an output (whether the email is considered spam or not). The training dataset would include emails labeled as spam or not spam, and the algorithm would use features of each email (contents, header, sender domain, etc.) to learn the typical attributes of a spam email.

This model-building phase is called "training." Once trained with existing data, the model can perform predictions with new, previously unseen data, which is called "inferencing," "evaluation," or "scoring."

There is a huge ecosystem of ML frameworks and tools for training and evaluting models. If you'd like to build ML and AI models, check out the [Microsoft AI platform](https://azure.microsoft.com/en-us/overview/ai-platform/). For more information about research and solutions, visit [Artifical Intelligence at Microsoft](https://www.microsoft.com/ai).

## What is Windows ML?

![Windows machine learning](images/winml-graphic.png)

Windows ML provides hardware-accelerated, on-device evaluation of trained machine learning models on Windows 10 devices. With abstractions for both ML models and hardware specifications, Windows ML offers flexibility to developers for building intelligent applications on the edge.

To learn more about how to develop with Windows ML, continue onto [Windows ML overview](overview.md).