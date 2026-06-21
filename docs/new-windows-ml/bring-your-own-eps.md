---
title: Bring your own EPs to Windows ML
description: Learn how to use non-Windows ML execution provider binaries with Windows ML.
ms.date: 06/15/2026
ms.topic: how-to
---

# Bring your own EPs to Windows ML

When you cannot rely on [Windows ML EPs](./supported-execution-providers.md) — for example, on managed enterprise devices where Windows Update is restricted, in offline environments, or when you need strict version control — you can carry execution provider binaries directly in your project.

For guidance on choosing between the Windows ML EPs and the bring-your-own approach, see [Windows ML EPs vs. bring-your-own](./windows-ml-eps-vs-bring-your-own.md).

## Prerequisites

The execution provider you reference must be compatible with the ORT version that ships with the Windows ML version your app targets. See [ONNX Runtime versions in Windows ML](./onnx-versions.md) to know which version of ORT is in Windows ML.

## Known EP packages

| EP | Package |
|---|---|
| NvTensorRtRtx (NVIDIA) | [`NVIDIA/TensorRT-RTX-EP-ABI`](https://github.com/NVIDIA/TensorRT-RTX-EP-ABI) |
| OpenVINO (Intel) | [`Intel.ML.OnnxRuntime.EP.OpenVINO`](https://www.nuget.org/packages/Intel.ML.OnnxRuntime.EP.OpenVINO) |
| QNN (Qualcomm) | [`Qualcomm.ML.OnnxRuntime.QNN`](https://www.nuget.org/packages/Qualcomm.ML.OnnxRuntime.QNN) |

## Register the EP with Windows ML ONNX Runtime

For execution providers you brought yourself, you will simply register the execution provider using the existing ONNX APIs, pointing to your execution provider DLL path.

### [C#](#tab/csharp)

```csharp
// Point ORT at the EP library you shipped
// The path depends on how you package and ship the EP
OrtEnv.Instance().RegisterExecutionProviderLibrary("XYZExecutionProvider", "XYZ.EP.dll");

// And then inference using ORT
...
```

### [C++](#tab/cpp)

```cpp
// Point ORT at the EP library you shipped
// The path depends on how you package and ship the EP
env.RegisterExecutionProviderLibrary("XYZExecutionProvider", L"XYZ.EP.dll");

// And then inference using ORT
...
```

---

## App distribution considerations

When bundling EP packages:

- Each EP package adds approximately 80 MB or more to your app package size.
- You are responsible for updating EP packages when new versions are released.
- EP binaries must be included in your app package or installer — they are not downloaded at runtime.

For a comparison of the bundle-vs-Windows ML EP tradeoffs, see [Windows ML EPs vs. bring-your-own](./windows-ml-eps-vs-bring-your-own.md).

## See also

- [Windows ML EPs vs. bring-your-own](./windows-ml-eps-vs-bring-your-own.md)
- [ONNX Runtime versions in Windows ML](./onnx-versions.md)
- [Accelerate AI models](./accelerate-ai-models.md)
- [Windows ML execution providers](./supported-execution-providers.md)
