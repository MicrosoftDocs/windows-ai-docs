---
author: rosanevallim
title: Executing multiple ML models in a chain
description: This tutorial shows how to sequentially execute multiple machine learning models with the highest GPU performance
ms.author: rovalli
ms.date: 08/22/2018
ms.topic: article
ms.prod: windows
ms.technology: desktop
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Executing multiple ML models in a chain

Windows ML supports high-performance load and execution of model chains by carefully optimizing its GPU path. Model chains are defined by two or more models that execute sequentially, where the outputs of one model becomes the inputs to the next model down the chain. 
In order to explain how to efficiently chain models with Windows ML, let's use the FNS_Candy.onnx model as a toy example. You can find this model in the FNS_Candy sample folder in our GitHub.

Let's say we want to chain two identical FNS_Candy models, where we would provide an image to the first model, let it compute the outputs, and then pass that transformed image to another instance of FNS_Candy, producing a final image.  In a real word scenario you most likely would use two different models, but this should be enough to illustrate the concepts.

1. First, let's load the FNS_Candy model so that we can use it.

  ```cpp
  std::wstring filePath = FileHelpers::GetModulePath() + L "fns-candy.onnx"; TODO: confirm model name
  LearningModel model = LearningModel::LoadFromFilePath(filepath);
    ```

2. Then, let's create two identical sessions on the device's default GPU using the same model as input parameter. 


  ```cpp
  LearningModelSession session1(model, LearningModelDevice(LearningModelDeviceKind::DirectX));
  LearningModelSession session2(model, LearningModelDevice(LearningModelDeviceKind::DirectX));
    ```

!Note: in order to rip the performance benefits of chaining, you need to create identical GPU sessions for all of your models. Not doing so would incur in additional data movement out of the GPU and into the CPU, which would reduce performance.





