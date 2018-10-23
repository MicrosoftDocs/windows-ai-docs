---
author: eliotcowley
title: WinMLRunner
description: This section contains documentation for WinMLRunner tool.
ms.author: elcowle
ms.date: 10/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, WinMLRunner
ms.localizationpriority: medium
---

# WinMLRunner

The WinMLRunner tool is a command-line tool that can run .onnx or .pb models where the input and output variables are tensors or images. It will attempt to load, bind, and evaluate a model and output error messages if these steps were unsuccessful. It will also capture performance measurements on the GPU and/or CPU. If using the performance flag, the GPU, CPU and wall-clock times for loading, binding, and evaluating and the CPU and GPU memory usage during evaluation will print to the command line and to a CSV file.

This tool is useful because it lets you run the WinML APIs against a model and see if it hits any issues before trying to integrate it into an application.

You can download the tool and find more information about it on [GitHub](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLRunner).

## Syntax

Once you've downloaded WinMLRunner, open the solution in Visual Studio, build it, and run the executable in the command line using the following syntax:

### Required command-line arguments (choose one)

| Argument | Description |
|----------|-------------|
| -model &lt;path&gt; | Fully qualified path to a .onnx or .pb model file. |
| -folder &lt;path&gt; | Fully qualifed path to a folder with .onnx and/or .pb models; will run all of the models in the folder. |

### Optional command-line arguments

| Argument | Description |
|----------|-------------|
| -perf | Captures GPU, CPU, and wall-clock time measurements. | 
| -iterations &lt;int&gt; | Number of times to evaluate the model when capturing performance measurements. |
| -CPU | Will create a session on the CPU. |
| -GPU | Will create a session on the GPU. |
| -GPUHighPerformance | Will create a session with the most powerful GPU device available. |
| -GPUMinPower | Will create a session with the GPU with the least power. |
| -CPUBoundInput | Will bind the input to the CPU. |
| -GPUBoundInput | Will bind the input to the GPU. |
| -BGR | Will load the input as a BGR image. |
| -RGB | Will load the input as an RGB image. |
| -tensor | Will load the input as a tensor. |
| -input &lt;image/CSV path&gt; | Will bind image/data from CSV to model. |
| -output &lt;CSV path&gt; | Path to the CSV where the perf results will be written. |
| -IgnoreFirstRun | Will ignore the first run in the perf results when calculating the average. |
| -silent | Silent mode (only errors will be printed to the console). |
| -debug | Will start a trace logging session. |

[!INCLUDE [help](includes/get-help.md)]