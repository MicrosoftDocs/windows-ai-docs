---
title: Windows ML execution providers
description: Learn which ONNX Runtime execution providers are available in Windows ML for accelerating local AI models across Windows PCs, and see their release history.
ms.date: 04/28/2026
ms.topic: how-to
---

# Windows ML execution providers

Windows ML provides execution providers for accelerating inference on NPU, GPU, and CPU. To learn more about accelerating inference, see [Accelerate AI models](./accelerate-ai-models.md).

## Requirements summary

| Execution provider | Hardware | Minimum OS |
|---|---|---|
| CPU | Any x64/ARM64 CPU | Windows 10 19041+ |
| DirectML | Any DirectX 12 compatible GPU | Windows 10 19041+ |
| NvTensorRtRtx (NVIDIA) | NVIDIA RTX 30XX and above | Windows 11 24H2+ |
| QNN (Qualcomm) | Qualcomm Snapdragon X Elite/Plus | Windows 11 24H2+ |

## Included execution providers

The following execution providers are included with the ONNX Runtime that ships with Windows ML:

* CPU
* [DirectML](https://onnxruntime.ai/docs/execution-providers/DirectML-ExecutionProvider.html) (legacy)

## Available execution providers

The execution providers listed below are available on **Windows 11 PCs running version 24H2 (build 26100) or greater** (depending on device and driver compatibility) for dynamic download via the Windows ML `ExecutionProviderCatalog` APIs. To use these providers, see [Install Windows ML EPs](./initialize-execution-providers.md) and [Register Windows ML EPs](./register-execution-providers.md). Updated versions of the execution providers are made available via [Windows Update's optional nonsecurity preview releases, a.k.a. "D week releases"](/windows/deployment/update/release-cycle#optional-nonsecurity-preview-release).

### [Windows ML 2.x](#tab/winml2)

The following execution providers are available to developers using [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) or [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) version `2.x`:

| Execution provider | Current version and release date | Upcoming version and planned release dates |
|---|---|---|
| [MIGraphX (AMD)](#migraphx-amd) | MSIX: `1.8.55.0`<br/>Released: `2026 4D` | MSIX: `1.8.56.0`<br/>GPU EP: Ver49<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` |
| [NvTensorRtRtx (NVIDIA)](#nvtensorrtrtx-nvidia) | MSIX: `0.0.28.0`<br/>Released: `2026 4D` | MSIX: `0.0.33.0`<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` |
| [OpenVINO (Intel)](#openvino-intel) | MSIX: `1.8.69.0`<br/>OpenVINO: `2026.0`<br/>Released: `2026 3D` | MSIX: `1.8.79.0`<br/>OpenVINO: `2026.1`<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` |
| [QNN (Qualcomm)](#qnn-qualcomm) | MSIX: `2.2420.43.0`<br/>QAIRT: `2.42`<br/>Released: `2026 4D` | MSIX: `2.2450.47.0`<br/>QAIRT: `2.45`<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` |
| [VitisAI (AMD)](#vitisai-amd) | MSIX: `1.8.59.0`<br/>Released: `2026 4D` | MSIX: `1.8.62.0`<br/>EP: 2705<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` |

### [Windows ML 1.8.x](#tab/winml1-8)

The following execution providers are available to developers using [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) version `1.8.x`:

| Execution provider | Current version and release date | Upcoming version and planned release dates | Required [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) `1.8.x` |
|---|---|---|---|
| [MIGraphX (AMD)](#migraphx-amd) | MSIX: `1.8.55.0`<br/>Released: `2026 4D` | MSIX: `1.8.56.0`<br/>GPU EP: Ver49<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` | `1.8.2109` or greater |
| [NvTensorRtRtx (NVIDIA)](#nvtensorrtrtx-nvidia) | MSIX: `1.8.24.0`<br/>Released: `2026 2D` | | Any `1.8.x` version |
| [OpenVINO (Intel)](#openvino-intel) | MSIX: `1.8.69.0`<br/>OpenVINO: `2026.0`<br/>Released: `2026 3D` | MSIX: `1.8.79.0`<br/>OpenVINO: `2026.1`<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` | Any `1.8.x` version |
| [QNN (Qualcomm)](#qnn-qualcomm) | MSIX: `1.8.30.0`<br/>QAIRT: `2.40.0.251030`<br/>Released: `2026 1D` | | Any `1.8.x` version |
| [VitisAI (AMD)](#vitisai-amd) | MSIX: `1.8.59.0`<br/>Released: `2026 4D` | MSIX: `1.8.62.0`<br/>EP: 2705<br/>Insiders: `2026 4E`<br/>GA: `2026 5D` | Any `1.8.x` version |

---

Before your app uses an execution provider, please be sure to read the licenses corresponding to the execution provider.

> [!NOTE]
> Only the **current version** listed for each execution provider is supported. Upcoming versions are in preview and not guaranteed to be released. Past versions are shown for release history only.

[!INCLUDE [Release date notes](./includes/windows-update-release-dates-note.md)]

### MIGraphX (AMD)

**Current and upcoming releases:** See the [Available execution providers](#available-execution-providers) table above.

**Execution provider info**

* **EpName**: `"MIGraphXExecutionProvider"`
* **Requirements**:
  * GPU with version 25.10.13.09 (exactly)
  * *This execution provider is not supported for GenAI scenarios today.*
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/MIGraphX-ExecutionProvider.html)
* **Support**: [Support](https://github.com/ROCm/AMDMIGraphX/issues)
* **License terms**: [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html)

<details><summary><strong>Past releases</strong></summary>

| Version | Windows Update release | Release notes |
|--|--|--|
| 1.8.51.0 | 2026 3D | |
| 1.8.43.0 | 2026 1D | |
| 1.8.35.0 | 2025 11D | |

</details>

### NvTensorRtRtx (NVIDIA)

**Current and upcoming releases:** See the [Available execution providers](#available-execution-providers) table above.

**Execution provider info**

* **EpName**: `"NvTensorRtRtxExecutionProvider"`
* **Requirements**:
  * NVIDIA GeForce RTX 30XX and above with minimum recommended driver version 32.0.15.5585 + Cuda version 12.5
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/TensorRTRTX-ExecutionProvider.html)
* **Support**: [Support](https://github.com/NVIDIA/TensorRT-RTX/issues)
* **License terms**: [NVIDIA SOFTWARE LICENSE AGREEMENT](https://docs.nvidia.com/deeplearning/tensorrt-rtx/latest/reference/sla.html) and [License Agreement for NVIDIA Software Development Kits — EULA](https://docs.nvidia.com/cuda/eula/index.html)

<details><summary><strong>Past releases</strong></summary>

#### [Windows ML 2.x](#tab/winml2)

| Version | Windows Update release | Release notes |
|--|--|--|
| 0.0.26.0 | 2026 3D | |

#### [Windows ML 1.8.x](#tab/winml1-8)

| Version | Windows Update release | Release notes |
|--|--|--|
| 1.8.24.0 | 2026 2D | Bug fix for WebNN. |
| 1.8.22.0 | 2026 1D | |
| 1.8.14.0 | 2025 9D | |

---

</details>

### OpenVINO (Intel)

**Current and upcoming releases:** See the [Available execution providers](#available-execution-providers) table above.

**Execution provider info**

* **EpName**: `"OpenVINOExecutionProvider"`
* **Requirements**:
  * CPU: Intel TigerLake (11th Gen) and above with min recommended driver 32.0.100.9565
  * GPU: Intel AlderLake (12th Gen) and above with min recommended driver 32.0.101.1029
  * NPU: Intel ArrowLake (15th Gen) and above with min recommended driver 32.0.100.4239
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/OpenVINO-ExecutionProvider.html)
* **Support**: [Support](https://github.com/openvinotoolkit/openvino/issues)
* **License terms**: [Intel OBL Distribution Commercial Use License Agreement v2025.02.12](https://cdrdv2.intel.com/v1/dl/getContent/849090?explicitVersion=true)

<details><summary><strong>Past releases</strong></summary>

For release notes of each OpenVINO version, see OpenVINO's [2026.x](https://docs.openvino.ai/2026/about-openvino/release-notes-openvino.html) and [2025.x](https://docs.openvino.ai/2025/about-openvino/release-notes-openvino.html) release notes.

| Version | Windows Update release | OpenVINO version |
|--|--|--|
| 1.8.63.0 | 2026 1D | OpenVINO 2025.4.1 |
| 1.8.26.0 | 2025 11D | OpenVINO 2025.3 |
| 1.8.18.0 | 2025 10D | OpenVINO 2025.3 |
| 1.8.15.0 | 2025 9D | |

</details>

### QNN (Qualcomm)

**Current and upcoming releases:** See the [Available execution providers](#available-execution-providers) table above.

**Execution provider info**

* **EpName**: `"QNNExecutionProvider"`
* **Requirements**:
  * Snapdragon(R) X Elite - X1Exxxxx
    * Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above
  * Snapdragon(R) X Plus - X1Pxxxxx
    * Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/QNN-ExecutionProvider.html)
* **Support**: [Support](https://www.qualcomm.com/support)
* **License terms**: To view the QNN License, [download the Qualcomm® Neural Processing SDK](https://www.qualcomm.com/developer/software/neural-processing-sdk-for-ai), extract the ZIP, and open the *LICENSE.pdf* file.

<details><summary><strong>Past releases</strong></summary>

#### [Windows ML 2.x](#tab/winml2)

No past releases yet.

#### [Windows ML 1.8.x](#tab/winml1-8)

For release notes of each QNN QAIRT SDK version, see [Qualcomm AI Runtime (QAIRT) SDK Release Notes](https://docs.qualcomm.com/doc/80-63442-10/topic/release_notes.html).

| Version | Windows Update release | QNN QAIRT SDK version |
|--|--|--|
| 1.8.21.0 | 2025 11D | QNN 2.39 |
| 1.8.14.0 | 2025 10D | |
| 1.8.13.0 | 2025 9D | |

---

</details>

### VitisAI (AMD)

**Execution provider info**

* **EpName**: `"VitisAIExecutionProvider"`
* **Requirements**:
  * **Min**: Adrenalin Edition 25.6.3 with NPU driver 32.00.0203.280
  * **Max**: Adrenalin Edition 25.9.1 with NPU driver 32.00.0203.297
* **Documentation**: [Documentation](https://onnxruntime.ai/docs/execution-providers/Vitis-AI-ExecutionProvider.html)
* **Support**: [Support](https://www.amd.com/en/developer/resources/ryzen-ai-software.html)
* **License terms**: [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html)

<details><summary><strong>Past releases</strong></summary>

| Version | Windows Update release | Release notes |
|--|--|--|
| 1.8.55.0 | 2026 3D | |
| 1.8.53.0 | 2026 2D | Support for Procyon V2 Models with 13% improved score on GPT2 Machine in Turbo mode. Bug fix for disk space consumption on system C drive after restart. |
| 1.8.50.0 | 2026 1D | |
| 1.8.43.0 | 2025 11D (Windows Insiders) | |
| 1.8.31.0 | 2025 11A (Windows Insiders) | |
| 1.8.26.0 | 2025 10D | |
| 1.8.24.0 | 2025 9D | |

</details>

## See also

* [Install Windows ML EPs](./initialize-execution-providers.md)
* [Register Windows ML EPs](./register-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Install and deploy Windows ML](./distributing-your-app.md)
