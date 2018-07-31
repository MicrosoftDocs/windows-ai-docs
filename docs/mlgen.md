---
author: serenaz
title: Automatic code generation with mlgen
description:
ms.author: sezhen
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Automatic code generation with mlgen

> [!VIDEO https://www.youtube.com/embed/8MCDSlm326U]

Windows ML's code generator `mlgen` creates an interface (C# or C++/WinRT) with wrapper classes that call the [Windows ML API](/uwp/api/windows.ai.machinelearning) for you, allowing you to easily load, bind, and evaluate the model in your project.

For UWP developers, `mlgen` is natively integrated with [Visual Studio (version 15.7)](https://developer.microsoft.com/windows/downloads). Inside your Visual Studio project, simply add your ONNX file to your project’s Assets, and VS will generate Windows ML wrapper classes in a new interface file.

For other workflows, or older versions of VS, you can use the command line tool `mlgen.exe`, which comes with the Windows SDK, to generate Windows ML wrapper classes. The tool is located in `(SDK_root)\bin\<version>\x64` or `(SDK_root)\bin\<version>\x86`, where SDK_root is the SDK installation directory. To run the tool, use the command below.

```
mlgen -i INPUT-FILE -l LANGUAGE -n NAMESPACE [-o OUTPUT-FILE]
```

Input parameters definition:

- `INPUT-FILE`: the ONNX model file
- `LANGUAGE`: CPP or CS
- `NAMESPACE`: the namespace of the generated code
- `OUTPUT-FILE`: file path where the generated code will be written to. If OUTPUT-FILE is not specified, the generated code is written to the standard output

The tool will output a file containing the interface classes, which you can then add to your VS project.

**Note**: To make sure your model builds when you compile your application, right click on the `.onnx` file, and select **Properties**. For **Build Action**, select **Content**.

Using the interface's generated wrapper classes, you'll follow the load, bind, and evaluate pattern to integrate your ML model into your app. For an example of how to load, bind, and evaluate a model in your app, see our [Get Started (UWP)](get-started-uwp.md) tutorial.