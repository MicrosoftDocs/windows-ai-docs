---
title: Supported execution providers in Windows ML
description: Learn which ONNX Runtime execution providers Windows ML supports for running local AI models across Windows PCs.
ms.date: 08/13/2025
ms.topic: how-to
---

# Supported execution providers in Windows ML

Windows ML supports the following execution providers. To learn more about execution providers, [see the ONNX Runtime docs](https://onnxruntime.ai/docs/execution-providers/).

## Included execution providers

The following execution providers are included with the ONNX Runtime that ships with Windows ML:

* CPU
* [DirectML](https://onnxruntime.ai/docs/execution-providers/DirectML-ExecutionProvider.html)

## Available execution providers

The following execution providers are available (depending on device and driver compatibility) for dynamic download and registration via the Windows ML `ExecutionProviderCatalog` APIs (see [Initialize execution providers](./initialize-execution-providers.md)):

* [AMD - Vitis AI](https://onnxruntime.ai/docs/execution-providers/Vitis-AI-ExecutionProvider.html)
* [Intel - OpenVINO™](https://onnxruntime.ai/docs/execution-providers/OpenVINO-ExecutionProvider.html)
* [Qualcomm - QNN](https://onnxruntime.ai/docs/execution-providers/QNN-ExecutionProvider.html)
* [NVIDIA - TensorRT RTX](https://onnxruntime.ai/docs/execution-providers/TensorRTRTX-ExecutionProvider.html)

## See also

* [Initialize execution providers](./initialize-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)