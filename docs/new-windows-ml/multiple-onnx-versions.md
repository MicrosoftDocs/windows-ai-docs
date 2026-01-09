---
title: Use multiple versions of ONNX Runtime in an app
description: Learn how to use multiple versions of the ONNX runtime in your application while using Windows ML.
ms.date: 01/07/2026
ms.topic: how-to
---

# Use multiple versions of ONNX Runtime in an app

Some apps might need to use multiple different versions of ONNX Runtime. For example, some of your models might require specific ONNX Runtime versions, or your app might run unknown plugins from developers where the plugin developer needs to control their own ONNX Runtime version.

In these cases, you can run Windows ML's ONNX Runtime alongside another ONNX Runtime by running Windows ML in a separate process.

As an example of how to do this, say you wanted to take advantage of Windows ML's ability to run SqueezeNet. You would first build [this sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cs/CSharpConsoleDesktop).

This sample accepts an image file as a command-line argument and outputs its interpretation of the image contents.

Within your own application code, you can launch that sample's process, collect its output, and use it in your app.

```csharp
// Spin up another process which uses Windows ML
var process = new System.Diagnostics.Process();
process.StartInfo.FileName = @"CSharpConsoleDesktop.exe";
process.StartInfo.Arguments = "--ep_policy CPU --image_path myimage.jpg";
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

## See also

* [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md)
* [Run ONNX models using the ONNX Runtime included in Windows ML](./run-onnx-models.md)