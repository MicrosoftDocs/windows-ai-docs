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

# Add the model

Windows ML's code generator creates an interface that allows you to easily interact with the model in your app. The generated interface includes wrapper classes that call the [Windows ML API](/uwp/api/windows.ai.machinelearning.preview) for you, allowing you to easily load, bind, and evaluate the model in your project. The code generator currently supports both C# and C++/WinRT.

For UWP developers, Windows ML's automatic code generator is natively integrated with [Visual Studio (version 15.7)](https://developer.microsoft.com/windows/downloads). Inside your Visual Studio project, simply add your ONNX file to your project’s Assets, and VS will generate Windows ML wrapper classes in a new interface file.


For other workflows, or older versions of VS, you can also use the command line tool `mlgen.exe`, which comes with the Windows SDK, to generate Windows ML wrapper classes. The tool is located in `(SDK_root)\bin\<version>\x64` or `(SDK_root)\bin\<version>\x86`, where SDK_root is the SDK installation directory. To run the tool, use the command below.

```
mlgen -i INPUT-FILE -l LANGUAGE -n NAMESPACE [-o OUTPUT-FILE]
```

Input parameters definition:

- `INPUT-FILE`: the ONNX model file
- `LANGUAGE`: CPP or CS
- `NAMESPACE`: the namespace of the generated code
- `OUTPUT-FILE`: file path where the generated code will be written to. If OUTPUT-FILE is not specified, the generated code is written to the standard output

The tool will output a file containing the interface classes, which you can then add to your VS project. To learn how to use the generated code in your app, see [load, bind, and evaluate](load-bind-evaluate.md).

**Note**: To make sure your model builds when you compile your application, right click on the `.onnx` file, and select **Properties**. For **Build Action**, select **Content**.
