---
title: Load a model
description: Learn how to load an ONNX model into your application for Windows Machine Learning to use.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
---

# Load a model

> [!IMPORTANT]
> Windows Machine Learning requires ONNX models, version 1.2 or higher.

Once you [get a trained ONNX model](get-onnx-model.md), you'll distribute the .onnx model file(s) with your app. You can include the .onnx file(s) in your APPX package, or, for desktop apps, they can be anywhere your app can access on the hard drive.

There are several ways to load a model using static methods on the [LearningModel](/uwp/api/windows.ai.machinelearning.learningmodel) class:

* [LearningModel.LoadFromStreamAsync](/uwp/api/windows.ai.machinelearning.learningmodel.loadfromstreamasync)
* [LearningModel.LoadFromStream](/uwp/api/windows.ai.machinelearning.learningmodel.loadfromstream)
* [LearningModel.LoadFromStorageFileAsync](/uwp/api/windows.ai.machinelearning.learningmodel.loadfromstoragefileasync)
* [LearningModel.LoadFromFilePath](/uwp/api/windows.ai.machinelearning.learningmodel.loadfromfilepath)

The **LoadFromStream*** methods allow applications to have more control over where the model comes from. For example, an app could choose to have the model encrypted on disk and decrypt it only in memory prior to calling one of the **LoadFromStream*** methods. Other options include loading the model stream from a network share or other media.

> [!TIP]
> Loading a model can take some time, so take care not to call a **Load*** method from your UI thread.

The following example shows how you can load a model into your application:

```cs
private async LearningModel LoadModelAsync(string modelPath)
{
    // Load and create the model
    var modelFile = await StorageFile.GetFileFromApplicationUriAsync(
        new Uri(modelPath));

    LearningModel model =
        await LearningModel.LoadFromStorageFileAsync(modelFile);

    return model;
}
```

## See also

* Next: [Create a session](create-a-session.md)

[!INCLUDE [help](../includes/get-help.md)]
