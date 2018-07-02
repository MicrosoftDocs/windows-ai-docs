---
author: serenaz
title: How to use the Windows ML APIs
description: Learn how to use the Windows.AI.MachineLearning APIs to load, bind, and evaluate models in your Windows applications.
ms.author: sezhen
ms.date: 06/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Use the Windows ML APIs

In WIndowsML a developer technically does 3 major activities: 
1. Load 
2. Bind 
3. Eval 

Definitions:

* Model: A fully trained ML model that has been saved/exported into the ONNX file format. 
* Feature: an input or output for a ML model 
* FeatureDescriptor: a schema definition for a feature 
* FeatureValue: an actual instantiated value for a feature 
* Tensor: a strongly typed multi-dimensional array. 
* Device: Where you want to run the ML model. You can either run them on the CPU, or you can run them on a DirectX device. 
* Session: everything you need to run an ML model. The model, with a chosen device, gives you a session. 
* Bindings: where you bind your FeatureValue’s to the inputs and outputs for that session. Any transformations that need to be done on the inputs are done during Bind(). 
* Evaluation: the act of running the model. Processing the inputs and producing the outputs. 
* Windows machine learning is broken into 3 major steps: load/bind/eval 