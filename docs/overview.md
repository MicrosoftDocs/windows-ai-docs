---
author: serenaz
title: Windows ML overview
description: Learn about Windows Machine Learning and how to develop with Windows ML.
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, windows machine learning
ms.localizationpriority: medium
---
# Windows ML overview

Windows ML provides hardware-accelerated, on-device evaluation of trained machine learning models on Windows 10 devices. With the Windows ML APIs, you can use machine learning within Windows applications, bringing intelligence to the edge. In this overview, we'll cover how you can develop intelligent edge applications with Windows ML.

## Video summary

> [!VIDEO https://www.youtube.com/embed/8MCDSlm326U]

## How to develop with Windows ML

![windows ML developer flow](images/winmlstory.png)

1. From any training environment, you'll need an ONNX model.
2. Add the ONNX model file(s) to your Windows app.
3. Call the Windows ML APIs in your application code.
4. Evaluate on any Windows 10 device!

## ONNX models

In the huge ecosystem of ML frameworks and tools, [Open Neural Network Exchange](https://onnx.ai) (ONNX) provides an open format for ML models, allowing you to import and export models between various frameworks. ONNX defines an extensible computation graph model, as well as definitions of built-in operators and standard data types. ONNX is being co-developed by Microsoft, Amazon and Facebook as an open-source project.

To use Windows ML, you'll need a pre-trained machine learning model in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. Windows ML supports the v1.0 release of the ONNX format, which allows developers to use models produced by different training frameworks.

### 1. ONNX Galleries

For a list of publicly available ONNX models, see [ONNX Models](https://github.com/onnx/models) on GitHub.

Azure Model Gallery

### 2. Train your own ONNX model

Custom Vision, Azure ML

### 3. Convert existing models to ONNX

Many training frameworks already natively support ONNX models, and there are converter tools for many frameworks and libraries. To learn how to export from frameworks such as Caffe 2, PyTorch, CNTK, Chainer, and more, see [ONNX tutorials](https://github.com/onnx/tutorials) on GitHub.

You can also use [WinMLTools](https://pypi.org/project/winmltools/) to convert trained machine learning model to the ONNX format accepted by Windows ML. WinMLTools supports conversion from these formats:

- Core ML
- Scikit-Learn
- XGBoost
- LibSVM

To learn how to install and use WinMLTools, please see [Convert a model](conversion-samples.md).

With the Visual Studio Tools for AI extension, you can also use WinMLTools within the Visual Studio IDE for a more friendly, click-through experience to convert your models into ONNX format. To learn more, please visit [VS Tools for AI](https://github.com/Microsoft/vs-tools-for-ai/).

## Windows ML APIs

Install the [Windows 10 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk) - Build 17110 or higher.

### Automatic interface code generation

With an ONNX model file, Windows ML's code generator creates an interface to interact with the model in your app. The generated interface includes wrapper classes that represent the model, inputs, and outputs. The generated code calls the [Windows ML API](/uwp/api/windows.ai.machinelearning.preview) for you, allowing you to easily load, bind, and evaluate the model in your project. The code generator currently supports both C# and C++/CX.

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

### Windows ML APIs

Load, bind, evaluate.

C# and C++

## Next steps

Try creating your first Windows ML app with a step-by-step tutorial in [Get Started](get-started.md).