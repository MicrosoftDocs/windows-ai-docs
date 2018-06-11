---
author: serenaz
title: 
description: 
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, windows machine learning
ms.localizationpriority: medium
---

# How to develop with Windows ML

In this overview, we'll cover how to use Windows ML to bring machine learning to your Windows 10 apps and devices.

## Video summary

> [!VIDEO https://www.youtube.com/embed/8MCDSlm326U]

## How to develop with Windows ML

![windows ML developer flow](images/winmlstory.png)

1. From any training environment, you'll need to get an ML model in ONNX format.
2. Add the ONNX model file(s), and use Windows ML's automatic code generation, or call the Windows ML APIs.
3. Load, bind, and evaluate the model in your application code.
4. Run on any Windows 10 device!

## ONNX models

To use Windows ML, you'll need a pre-trained machine learning model in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools.

To get a pre-trained model in ONNX format, see [Get an ONNX model](get-onnx-model.md).

If you already have a trained model in a different format, such as Caffe2, CNTK, Chainer, Pytorch, or Tensorflow, then you can export to ONNX format with the [ONNX tutorials](https://github.com/onnx/tutorials) on GitHub.

You can also use [WinMLTools](https://pypi.org/project/winmltools/) to convert trained models to the ONNX format. WinMLTools supports conversion from these formats:

- Core ML
- Scikit-Learn
- XGBoost
- LibSVM

To learn how to install and use WinMLTools, please see [Convert a model](conversion-samples.md).

With the [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai/) extension, you can also use WinMLTools within the Visual Studio IDE for a more friendly, click-through experience to convert your models into ONNX format.

Once you have an ONNX model, you can integrate the model into your app either by using Windows ML's code generator or calling the Windows ML APIs directly.

## Automatic interface code generation

Windows ML's code generator `mlgen` creates an interface to interact with your ONNX model in your app's code. 

The generated interface includes wrapper classes that represent the model, inputs, and outputs. The generated code calls the [Windows ML API](/uwp/api/windows.ai.machinelearning.preview) for you, allowing you to easily [load, bind, and evaluate](integrate-model.md) the model in your project. The code generator currently supports both C# and C++/CX.

For UWP developers, Windows ML's automatic code generator is natively integrated with [Visual Studio](https://developer.microsoft.com/windows/downloads). Inside your Visual Studio project, simply add your ONNX file as an existing item, and VS will generate Windows ML wrapper classes in a new interface file.

You can also use the command line tool `mlgen.exe`, which comes with the Windows SDK, to generate Windows ML wrapper classes. The tool is located in `(SDK_root)\bin\<version>\x64` or `(SDK_root)\bin\<version>\x86`, where SDK_root is the SDK installation directory. To run the tool, use the command below.

```
mlgen -i INPUT-FILE -l LANGUAGE -n NAMESPACE [-o OUTPUT-FILE]
```

Input parameters definition:

- `INPUT-FILE`: the ONNX model file
- `LANGUAGE`: CPPCX or CS
- `NAMESPACE`: the namespace of the generated code
- `OUTPUT-FILE`: file path where the generated code will be written to. If OUTPUT-FILE is not specified, the generated code is written to the standard output

To learn how to use the generated code in your app, see [Integrate a model](integrate-model.md).

## Windows ML APIs

The Windows ML APIs are WinRT APIs and consummable in both C# and C++. You can call the APIs from your application's code, and follow the load, bind, evaluate pattern to integrate your model into your app.
