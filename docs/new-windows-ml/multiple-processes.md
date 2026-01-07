---
title: Manage multiple instances of the ONNX Runtime
description: Learn how to use multiple copies of the ONNX runtime in your application
ms.date: 12/19/2025
ms.topic: how-to
---

# Managing multiple instances of the ONNX Runtime

Sometimes, updating your app to use WinML’s ORT isn’t practical, for example if you rely on custom changes made to your ORT. In such cases, when you use WinML to add new AI features to your application, you’ll want to run WinML in a separate process.

As an example on how to do this, say you wanted to take advantage of WinML’s ability to run SqueezeNet. You would first build the [sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cs/CSharpConsoleDesktop)

This sample accepts an image file as a command-line argument and outputs its interpretation of the image contents.

Within your own application code, you can launch that process, collect its output, and use it in your app.

### [C#](#tab/csharp)
```csharp
// Spin up another process which uses Windows ML
var process = new System.Diagnostics.Process();
process.StartInfo.FileName = @"CSharpConsoleDesktop.exe";
process.StartInfo.Arguments = "--ep_policy CPU –image_path myimage.jpg");
process.StartInfo.RedirectStandardOutput = true;
process.StartInfo.UseShellExecute = false;
process.StartInfo.CreateNoWindow = true;

process.OutputDataReceived += (sender, e) =>
{
    // parse output
};

process.Start();
process.BeginOutputReadLine();
process.WaitForExit();
```

This method is effective for executing a model a single time which accepts a string as input and produces strings as output. For scenarios that require a persistent AI worker process or the exchange of data types other than strings, Windows offers a variety of [interprocess communication mechanisms](/windows/win32/ipc/interprocess-communications) to support these needs.
