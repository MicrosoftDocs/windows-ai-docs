---
title: Run LLMs and other generative models using ONNX Runtime and Windows ML
description: Learn how to use Windows Machine Learning (ML) to run local GenAI ONNX models (LLMs, speech-to-text) in your Windows apps.
ms.date: 01/13/2026
ms.topic: how-to
---

# Run LLMs and other generative models using ONNX Runtime and Windows ML

When using Generative AI (GenAI) models like Large Language Models (LLMs) or generative speech-to-text models in Windows ML, you can use the [ONNX Runtime GenAI Windows ML library](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntimeGenAI.WinML/) with Windows ML.

The GenAI API gives you an easy, flexible and performant way of running generative models on device. It implements the generative AI loop for ONNX models, including pre and post processing, inference with ONNX Runtime, logits processing, search and sampling, KV cache management, and grammar specification for tool calling.

## Supported models

The GenAI API supports various LLMs and generative speech-to-text models.

See the [onnxruntime-genai GitHub](https://github.com/microsoft/onnxruntime-genai) page for more details.

## Installation

Install the [ONNX Runtime GenAI Windows ML NuGet package](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntimeGenAI.WinML/) in your project.

## Usage

See the [ONNX Runtime GenAI docs](https://onnxruntime.ai/docs/genai/) for more details.

* [C# API reference](https://onnxruntime.ai/docs/genai/api/csharp.html)
* [C++ API reference](https://onnxruntime.ai/docs/genai/api/cpp.html)

## See also

* [Run ONNX models](./run-onnx-models.md)
* [Use ONNX APIs in Windows ML](./use-onnx-apis.md)
* [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md)
* [Install execution providers](./initialize-execution-providers.md)
* [Register execution providers](./register-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)
