---
title: Accelerate AI models with Windows ML
description: Learn how Windows ML accelerates ONNX model inference on NPUs, GPUs, and CPUs through hardware-tuned execution providers.
ms.date: 04/08/2026
ms.topic: overview
---

# Accelerate AI models with Windows ML

Windows ML accelerates inference across NPUs, GPUs, and CPUs by pairing the ONNX Runtime with hardware-tuned execution providers (EPs). To learn more about execution providers, see the [ONNX Runtime docs](https://onnxruntime.ai/docs/execution-providers/).

> [!NOTE]
> You're still responsible for optimizing your models for different hardware. Windows ML handles execution provider distribution, not model optimization. See [AI Toolkit](https://code.visualstudio.com/docs/intelligentapps/modelconversion) and the [ONNX Runtime Tutorials](https://onnxruntime.ai/docs/tutorials/) for more info on optimization.

## What is an execution provider?

An execution provider (EP) is a component that enables hardware-specific optimizations for machine learning (ML) operations. Execution providers abstract different compute backends (NPU, GPU, and CPU) and provide a unified interface for graph partitioning, kernel registration, and operator execution. To learn more, see the [ONNX Runtime docs](https://onnxruntime.ai/docs/execution-providers/).

## Two ways to get EPs

**Windows ML EPs:** Use the `ExecutionProviderCatalog` APIs to acquire Windows-certified EPs that go through a rigorous certification and regression testing process, and are automatically updated. See [Windows ML EPs](./supported-execution-providers.md) to learn more.

**Bring your own:** Obtain and reference EP binaries yourself, enabling support for offline environments, managed devices, or strict version-pinning requirements. See [Bring your own EPs](./bring-your-own-eps.md) to learn more.

See [Windows ML EPs vs. bring-your-own](./windows-ml-eps-vs-bring-your-own.md) for tradeoffs.

## Silicon-to-EP mapping

| Silicon | Execution providers | Typical use case |
|---|---|---|
| **NPU** | OpenVINO (Intel)<br>QNN (Qualcomm)<br>VitisAI (AMD) | Battery-efficient, sustained on-device inference on Copilot+ PCs |
| **GPU** | MIGraphX (AMD)<br>NvTensorRtRtx (NVIDIA)<br>OpenVINO (Intel)<br>QNN (Qualcomm)<br>DirectML (included - legacy) | High-throughput image/video/GenAI workloads |
| **CPU** | OpenVINO (Intel)<br>ORT CPU EP (included) | Universal fallback; low-latency for small models |

## See also

- [Windows ML execution providers](./supported-execution-providers.md) — EPs available through Windows ML
- [Windows ML EPs vs. bring-your-own](./windows-ml-eps-vs-bring-your-own.md) — choose the right EP sourcing strategy
- [Install Windows ML EPs](./initialize-execution-providers.md)
- [Register Windows ML EPs](./register-execution-providers.md)
- [Select execution providers](./select-execution-providers.md)
