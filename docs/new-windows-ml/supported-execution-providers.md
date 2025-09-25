---
title: Supported execution providers in Windows ML
description: Learn which ONNX Runtime execution providers Windows ML supports for running local AI models across Windows PCs.
ms.date: 09/16/2025
ms.topic: how-to
---

# Supported execution providers in Windows ML

Windows ML supports the following execution providers. To learn more about execution providers, [see the ONNX Runtime docs](https://onnxruntime.ai/docs/execution-providers/).

## Included execution providers

The following execution providers are included with the ONNX Runtime that ships with Windows ML:

* CPU
* [DirectML](https://onnxruntime.ai/docs/execution-providers/DirectML-ExecutionProvider.html)

## Available execution providers

The execution providers listed below are available (depending on device and driver compatibility) for dynamic download and registration via the Windows ML `ExecutionProviderCatalog` APIs (see [Initialize execution providers](./initialize-execution-providers.md)). Before you download an execution provider, please be sure to read the licenses corresponding to the execution provider.

Name (Vendor) | Requirements | License Terms
--|--|--
[`"NvTensorRtRtxExecutionProvider"`](https://onnxruntime.ai/docs/execution-providers/TensorRTRTX-ExecutionProvider.html)<br/>(Nvidia) | NVIDIA GeForce RTX 30XX and above with minimum recommended driver version 32.0.15.5585 + Cuda version 12.5 | [eula-12Aug2025.pdf](https://docs.nvidia.com/deeplearning/tensorrt-rtx/latest/_static/eula-12Aug2025.pdf)<br/>[License Agreement for NVIDIA Software Development Kits — EULA](https://docs.nvidia.com/cuda/eula/index.html)
[`"OpenVINOExecutionProvider"`](https://onnxruntime.ai/docs/execution-providers/OpenVINO-ExecutionProvider.html)<br/>(Intel) | CPU: Intel TigerLake (11th Gen) and above with min recommended driver 32.0.100.9565<br/>GPU: Intel AlderLake (12th Gen) and above with min recommended driver 32.0.101.1029 <br/>NPU: Intel ArrowLake (15th Gen) and above with min recommended driver 32.0.100.4239 | [Intel OBL Distribution Commercial Use License Agreement v2025.02.12](https://cdrdv2.intel.com/v1/dl/getContent/849090?explicitVersion=true)
[`"QNNExecutionProvider"`](https://onnxruntime.ai/docs/execution-providers/QNN-ExecutionProvider.html)<br/>(Qualcomm) | Snapdragon(R) X Elite - X1Exxxxx - Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above<br/>Snapdragon(R) X Plus - X1Pxxxxx - Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above | To view the QNN License, [download the Qualcomm® Neural Processing SDK](https://www.qualcomm.com/developer/software/neural-processing-sdk-for-ai), extract the ZIP, and open the *LICENSE.pdf* file.
[`"VitisAIExecutionProvider"`](https://onnxruntime.ai/docs/execution-providers/Vitis-AI-ExecutionProvider.html)<br/>(AMD) | **Min**: Adrenalin Edition 25.6.3 with NPU driver 32.00.0203.280<br/>**Max**: Adrenalin Edition 25.9.1 with NPU driver 32.00.0203.297 | No additional license required

## See also

* [Initialize execution providers](./initialize-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)