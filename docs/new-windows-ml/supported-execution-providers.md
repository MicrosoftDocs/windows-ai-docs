---
title: Windows ML execution providers
description: Learn which ONNX Runtime execution providers are available in Windows ML for accelerating local AI models across Windows PCs, and see their release history.
author: GrantMeStrength
ms.author: jken
ms.date: 08/26/2026
ms.topic: how-to
---

# Windows ML execution providers

Windows ML provides execution providers for accelerating inference on NPU, GPU, and CPU. To learn more about accelerating inference, see [Accelerate AI models](./accelerate-ai-models.md).

## Included execution providers

The following execution providers are included with the ONNX Runtime that ships with Windows ML:

* CPU
* [DirectML](https://onnxruntime.ai/docs/execution-providers/DirectML-ExecutionProvider.html) (legacy)

## Available execution providers

Unlike the CPU and DirectML providers, which ship in-box (see [Included execution providers](#included-execution-providers)), the execution providers below are not included with the runtime — they're downloaded on demand.

The execution providers listed below are available on **Windows 11 PCs running version 24H2 (build 26100) or greater** (depending on device and driver compatibility) for dynamic download via the Windows ML `ExecutionProviderCatalog` APIs. To use these providers, see [Install Windows ML EPs](./initialize-execution-providers.md) and [Register Windows ML EPs](./register-execution-providers.md). To see what version of each EP is currently available and info about upcoming releases, see [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases).

### [Windows ML 2.x](#tab/winml2)

The following execution providers are available to developers using [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) or [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) version `2.x`:

| Execution provider | EpName | Vendor |
|---|---|---|
| [MIGraphX](#migraphx-amd) | `MIGraphXExecutionProvider` | AMD |
| [NvTensorRtRtx](#nvtensorrtrtx-nvidia) | `NvTensorRtRtxExecutionProvider` | NVIDIA |
| [OpenVINO](#openvino-intel) | `OpenVINOExecutionProvider` | Intel |
| [QNN](#qnn-qualcomm) | `QNNExecutionProvider` | Qualcomm |
| [VitisAI](#vitisai-amd) | `VitisAIExecutionProvider` | AMD |
| [WebGPU (Experimental)](#webgpu-experimental) | `WebGpuExecutionProvider` | Microsoft |

### [Windows ML 1.8.x](#tab/winml1-8)

The following execution providers are available to developers using [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) version `1.8.x`:

| Execution provider | EpName | Vendor |
|---|---|---|
| [MIGraphX](#migraphx-amd) | `MIGraphXExecutionProvider` | AMD |
| [NvTensorRtRtx](#nvtensorrtrtx-nvidia) | `NvTensorRtRtxExecutionProvider` | NVIDIA |
| [OpenVINO](#openvino-intel) | `OpenVINOExecutionProvider` | Intel |
| [QNN](#qnn-qualcomm) | `QNNExecutionProvider` | Qualcomm |
| [VitisAI](#vitisai-amd) | `VitisAIExecutionProvider` | AMD |

---

Before your app uses an execution provider, please be sure to read the licenses corresponding to the execution provider.

### MIGraphX (AMD)

* **EpName**: `"MIGraphXExecutionProvider"`
* **Requirements**:
  * GPU with version 25.10.13.09 (exactly)
  * *This execution provider is not supported for GenAI scenarios today.*
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/MIGraphX-ExecutionProvider.html)
* **Support**: [Support](https://github.com/ROCm/AMDMIGraphX/issues)
* **License terms**: [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html)
* **Version and release history**: [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases)

### NvTensorRtRtx (NVIDIA)

* **EpName**: `"NvTensorRtRtxExecutionProvider"`
* **Requirements**:
  * NVIDIA GeForce RTX 30XX and above with minimum recommended driver version 32.0.15.5585 + Cuda version 12.5
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/TensorRTRTX-ExecutionProvider.html)
* **Support**: [Support](https://github.com/NVIDIA/TensorRT-RTX/issues)
* **License terms**: [NVIDIA SOFTWARE LICENSE AGREEMENT](https://docs.nvidia.com/deeplearning/tensorrt-rtx/latest/reference/sla.html) and [License Agreement for NVIDIA Software Development Kits — EULA](https://docs.nvidia.com/cuda/eula/index.html)
* **Version and release history**: [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases)

### OpenVINO (Intel)

* **EpName**: `"OpenVINOExecutionProvider"`
* **Requirements**:
  * CPU: 11th Generation Intel® Core™ processors (formerly code name Tiger Lake) or newer with at least 8GB memory
  * GPU: 12th Generation Intel® Core™ processors (formerly code name Alder Lake) or newer with at least 16GB memory
  * NPU: Intel® Core™ Ultra Series 1 processors (formerly code name Meteor Lake) or newer with at least 16GB memory
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/OpenVINO-ExecutionProvider.html)
* **Support**: [Support](https://github.com/openvinotoolkit/openvino/issues)
* **License terms**: [Intel OBL Distribution Commercial Use License Agreement v2025.02.12](https://cdrdv2.intel.com/v1/dl/getContent/849090?explicitVersion=true)
* **Version and release history**: [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases)

### QNN (Qualcomm)

* **EpName**: `"QNNExecutionProvider"`
* **Requirements**:
  * Snapdragon(R) X Elite - X1Exxxxx
    * Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above
  * Snapdragon(R) X Plus - X1Pxxxxx
    * Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/QNN-ExecutionProvider.html)
* **Support**: [Support](https://www.qualcomm.com/support)
* **License terms**: To view the QNN License, [download the Qualcomm® Neural Processing SDK](https://www.qualcomm.com/developer/software/neural-processing-sdk-for-ai), extract the ZIP, and open the *LICENSE.pdf* file.
* **Version and release history**: [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases)

### VitisAI (AMD)

* **EpName**: `"VitisAIExecutionProvider"`
* **Requirements**:
  * **Min**: Adrenalin Edition 25.6.3 with NPU driver 32.00.0203.280
  * **Max**: Adrenalin Edition 25.9.1 with NPU driver 32.00.0203.297
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/Vitis-AI-ExecutionProvider.html)
* **Support**: [Support](https://www.amd.com/en/developer/resources/ryzen-ai-software.html)
* **License terms**: [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html)
* **Version and release history**: [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases)

### WebGPU (experimental)

> [!IMPORTANT]
> WebGPU EP is **experimental** and requires installing experimental NuGet packages. It is not available in Windows ML 1.8.x. For full usage details, see [WebGPU EP](./webgpu-ep.md).

* **EpName**: `"WebGpuExecutionProvider"`
* **Package family name**: `Microsoft.WinML.ONNX.WebGPU.EP.2`
* **Minimum required [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) version**: [`2.4.66-preview`](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning/2.4.66-preview)
* **Requirements**:
  * Any DirectX 12–capable GPU with up-to-date drivers (roughly 11th-gen Intel integrated graphics or NVIDIA Turing-class and newer recommended)
* **Documentation**: [WebGPU EP](./webgpu-ep.md)
* **Support**: [Support](https://github.com/microsoft/WindowsML/issues)
* **License terms**: [WebGPU EP for Windows App SDK License](./webgpu-ep-license.md) and [ONNX Runtime License](https://github.com/microsoft/onnxruntime/blob/main/LICENSE)
* **Version and release history**: [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases)

<details><summary><strong>Past releases</strong></summary>

| MSIX version | WebGPU EP release notes |
|--|--|
| 0.2.1.0 | [`0.2.1`](https://github.com/microsoft/onnxruntime/releases/tag/plugin-ep-webgpu%2Fv0.2.1) |
| 0.1.0.0 | [`0.1.0`](https://github.com/microsoft/onnxruntime/releases/tag/plugin-ep-webgpu%2Fv0.1.0) |

</details>

## See also

* [Windows ML Execution Provider Releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases) (current, upcoming, and past versions)
* [Install Windows ML EPs](./initialize-execution-providers.md)
* [Register Windows ML EPs](./register-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Install and deploy Windows ML](./distributing-your-app.md)
