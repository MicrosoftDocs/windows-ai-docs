---
title: Supported execution providers in Windows ML
description: Learn which ONNX Runtime execution providers are available in Windows ML for running local AI models across Windows PCs, and see their release history.
ms.date: 01/06/2026
ms.topic: how-to
---

# Supported execution providers in Windows ML

Windows ML supports the following execution providers. To learn more about execution providers, [see the ONNX Runtime docs](https://onnxruntime.ai/docs/execution-providers/).

## Included execution providers

The following execution providers are included with the ONNX Runtime that ships with Windows ML:

* CPU
* [DirectML](https://onnxruntime.ai/docs/execution-providers/DirectML-ExecutionProvider.html)

## Available execution providers

The execution providers listed below are available on **Windows 11 PCs running version 24H2 (build 26100) or greater** (depending on device and driver compatibility) for dynamic download and registration via the Windows ML `ExecutionProviderCatalog` APIs (see [Initialize execution providers](./initialize-execution-providers.md)). Updated versions of the execution providers are made available via [Windows Update's optional nonsecurity preview releases, a.k.a. "D week releases"](/windows/deployment/update/release-cycle#optional-nonsecurity-preview-release).

Before your app uses an execution provider, please be sure to read the licenses corresponding to the execution provider.

### MIGraphX (AMD)

* **EpName**: `"MIGraphXExecutionProvider"`
* **Windows App SDK compatible versions**: 1.8.3 - 2.0.0-experimental3
* **Requirements**:
  * GPU with version 25.10.13.09 (exactly)
  * *This execution provider is not supported for GenAI scenarios today.*
* **Support**: [Support](https://github.com/ROCm/AMDMIGraphX/issues)
* **License terms**: [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html)

<br/>
<details><summary><strong>Release history</strong></summary>

[!INCLUDE [Release date notes](./includes/windows-update-release-dates-note.md)]

Version | Windows Update release
--|--
1.8.43.0 | 2026 1A (Windows Insiders)
1.8.35.0 | 2025 11D

</details>

### NvTensorRtRtx (NVIDIA)

* **EpName**: `"NvTensorRtRtxExecutionProvider"`
* **Windows App SDK compatible versions**: 1.8.1 - 2.0.0-experimental3
* **Requirements**:
  * NVIDIA GeForce RTX 30XX and above with minimum recommended driver version 32.0.15.5585 + Cuda version 12.5
* **Support**: [Support](https://github.com/NVIDIA/TensorRT-RTX/issues)
* **License terms**: [eula-12Aug2025.pdf](https://docs.nvidia.com/deeplearning/tensorrt-rtx/latest/_static/eula-12Aug2025.pdf) and [License Agreement for NVIDIA Software Development Kits — EULA](https://docs.nvidia.com/cuda/eula/index.html)

<br/>
<details><summary><strong>Release history</strong></summary>

[!INCLUDE [Release date notes](./includes/windows-update-release-dates-note.md)]

Version | Windows Update release
--|--
1.8.22.0 | 2026 1A (Windows Insiders)
1.8.14.0 | 2025 9D

</details>

### OpenVINO (Intel)

* **EpName**: `"OpenVINOExecutionProvider"`
* **Windows App SDK compatible versions**: 1.8.1 - 2.0.0-experimental3
* **Requirements**:
  * CPU: Intel TigerLake (11th Gen) and above with min recommended driver 32.0.100.9565
  * GPU: Intel AlderLake (12th Gen) and above with min recommended driver 32.0.101.1029
  * NPU: Intel ArrowLake (15th Gen) and above with min recommended driver 32.0.100.4239
* **Support**: [Support](https://github.com/openvinotoolkit/openvino/issues)
* **License terms**: [Intel OBL Distribution Commercial Use License Agreement v2025.02.12](https://cdrdv2.intel.com/v1/dl/getContent/849090?explicitVersion=true)

<br/>
<details><summary><strong>Release history</strong></summary>

[!INCLUDE [Release date notes](./includes/windows-update-release-dates-note.md)]

Version | Windows Update release
--|--
1.8.63.0 | 2026 1A (Windows Insiders)
1.8.26.0 | 2025 11D
1.8.18.0 | 2025 10D
1.8.15.0 | 2025 9D

</details>

### QNN (Qualcomm)

* **EpName**: `"QNNExecutionProvider"`
* **Windows App SDK compatible versions**: 1.8.1 - 2.0.0-experimental3
* **Requirements**:
  * Snapdragon(R) X Elite - X1Exxxxx
    * Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above
  * Snapdragon(R) X Plus - X1Pxxxxx
    * Qualcomm(R) Hexagon(TM) NPU with minimum driver version 30.0.140.0 and above
* **Support**: [Support](https://www.qualcomm.com/support)
* **License terms**: To view the QNN License, [download the Qualcomm® Neural Processing SDK](https://www.qualcomm.com/developer/software/neural-processing-sdk-for-ai), extract the ZIP, and open the *LICENSE.pdf* file.

<br/>
<details><summary><strong>Release history</strong></summary>

[!INCLUDE [Release date notes](./includes/windows-update-release-dates-note.md)]

Version | Windows Update release
--|--
1.8.30.0 | 2026 1A (Windows Insiders)
1.8.21.0 | 2025 11D
1.8.14.0 | 2025 10D
1.8.13.0 | 2025 9D

</details>

### VitisAI (AMD)

* **EpName**: `"VitisAIExecutionProvider"`
* **Windows App SDK compatible versions**: 1.8.1 - 2.0.0-experimental3
* **Requirements**:
  * **Min**: Adrenalin Edition 25.6.3 with NPU driver 32.00.0203.280
  * **Max**: Adrenalin Edition 25.9.1 with NPU driver 32.00.0203.297
* **Support**: [Support](https://www.amd.com/en/developer/resources/ryzen-ai-software.html)
* **License terms**: [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html)

<br/>
<details><summary><strong>Release history</strong></summary>

[!INCLUDE [Release date notes](./includes/windows-update-release-dates-note.md)]

Version | Windows Update release
--|--
1.8.50.0 | 2026 1A (Windows Insiders)
1.8.43.0 | 2025 11D (Windows Insiders)
1.8.31.0 | 2025 11A (Windows Insiders)
1.8.26.0 | 2025 10D
1.8.24.0 | 2025 9D

</details>

## See also

* [Initialize execution providers](./initialize-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)
