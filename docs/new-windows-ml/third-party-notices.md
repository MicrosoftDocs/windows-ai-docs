---
title: Windows ML third-party notices
description: Review the third-party software licenses that apply to Windows ML execution providers and optional runtime components like Llama.cpp and CUDA.
author: andrewleader
ms.author: aleader
ms.date: 10/05/2026
ms.topic: reference
---

# Windows ML third-party notices

Windows ML includes optional, dynamically downloaded components such as execution providers and runtime libraries that are provided by third parties. Each of these components is governed by its own license terms, separate from the license that applies to Windows ML itself. Before your app uses any of the components listed on this page, read the corresponding license terms.

## Execution provider licenses

The following license terms apply to the execution providers described in [Windows ML execution providers](./supported-execution-providers.md).

| Execution provider | Vendor | License terms |
|--|--|--|
| MIGraphX | AMD | [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html) |
| NvTensorRtRtx | NVIDIA | [NVIDIA SOFTWARE LICENSE AGREEMENT](https://docs.nvidia.com/deeplearning/tensorrt-rtx/latest/reference/sla.html) and [License Agreement for NVIDIA Software Development Kits — EULA](https://docs.nvidia.com/cuda/eula/index.html) |
| OpenVINO | Intel | [Intel OBL Distribution Commercial Use License Agreement v2025.02.12](https://cdrdv2.intel.com/v1/dl/getContent/849090?explicitVersion=true) |
| QNN | Qualcomm | To view the QNN License, [download the Qualcomm® Neural Processing SDK](https://www.qualcomm.com/developer/software/neural-processing-sdk-for-ai), extract the ZIP, and open the *LICENSE.pdf* file. |
| VitisAI | AMD | [Ryzen AI Licensing Information](https://ryzenai.docs.amd.com/en/latest/licenses.html) |
| WebGPU (experimental) | Microsoft | [WebGPU EP for Windows App SDK License](./webgpu-ep-license.md) and [ONNX Runtime License](https://github.com/microsoft/onnxruntime/blob/main/LICENSE) |

## Llama.cpp and GGML component licenses

Windows ML also makes use of the following third-party components for Llama.cpp and GGML based inference, including CUDA acceleration.

| Component | License | License terms |
|--|--|--|
| Llama.cpp Core | MIT | [llama.cpp LICENSE](https://github.com/ggml-org/llama.cpp/blob/master/LICENSE) |
| GGML | MIT | [ggml LICENSE](https://github.com/ggml-org/ggml/blob/master/LICENSE) |
| CUDA GGML backend | MIT | [llama.cpp LICENSE](https://github.com/ggml-org/llama.cpp/blob/master/LICENSE) |
| NVIDIA CUDA runtime dependencies | NVIDIA CUDA Toolkit EULA | [License Agreement for NVIDIA Software Development Kits — EULA](https://docs.nvidia.com/cuda/eula/) |

The NVIDIA CUDA runtime dependencies are CUDA Toolkit dependencies shipped as part of the CUDA GGML backend. This includes the dynamically loaded cuBLAS library, which your app acquires only after you set a flag indicating that you accept its license terms.

## See also

* [Windows ML execution providers](./supported-execution-providers.md)
* [Install Windows ML EPs](./initialize-execution-providers.md)
* [Register Windows ML EPs](./register-execution-providers.md)
