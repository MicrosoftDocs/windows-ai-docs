---
title: Use ONNX APIs in Windows ML
description: Learn how to use the ONNX APIs shipped in Windows Machine Learning (ML) to use local AI ONNX models in your Windows apps.
ms.date: 08/13/2025
ms.topic: how-to
---

# Use ONNX APIs in Windows ML

Windows Machine Learning (ML) includes a shared copy of the [ONNX Runtime](https://onnxruntime.ai/), including its APIs. That means when you install Windows ML via Windows App SDK, your app will have access to the full ONNX API surface.

To see what version of ONNX Runtime is included in specific Windows ML versions, see [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md).

This page covers how to use the ONNX APIs included in Windows ML.

## Prerequisites

* Follow all the steps in [Get started with Windows ML](./get-started.md).

## Namespaces / headers

The namespaces / headers for the ONNX APIs within Windows ML are as follows:

### [C#](#tab/csharp)

In C#, the namespaces of the ONNX APIs are the same as when using ONNX Runtime directly.

```csharp
using Microsoft.ML.OnnxRuntime;
```

### [C++](#tab/cppwinrt)

In C++, the ONNX Runtime headers are included in a `winml/` directory to avoid conflicts with other versions of ONNX Runtime.

```cppwinrt
#include <winml/onnxruntime_cxx_api.h>
```

If you have existing code that uses ONNX headers and do not want to update your includes, you can tell WinML to expose the headers without the `winml/` prefix by setting the **WinMLEnableDefaultOrtHeaderIncludePath** property in your project:

```xml
<PropertyGroup>
  <WinMLEnableDefaultOrtHeaderIncludePath>true</WinMLEnableDefaultOrtHeaderIncludePath>
</PropertyGroup>
```

Then, the headers will be the same as standalone ONNX Runtime:

```cppwinrt
#include <onnxruntime_cxx_api.h>
```

> [!NOTE]
> In the pre-GA releases of Windows ML, the C++ ONNX Runtime headers were prefixed by `win_` rather than within a `winml/` subdirectory, and the **WinMLEnableDefaultOrtHeaderIncludePath** property didn't exist.


### [Python](#tab/python)

In Python, the name of the ONNX Runtime module is the same as when using ONNX Runtime directly.

```python
import onnxruntime as ort
```

---

## APIs

The ONNX APIs are the same as when using ONNX Runtime directly. For example, to create an inference session:

### [C#](#tab/csharp)

```csharp
// Create inference session using compiled model
using InferenceSession session = new(compiledModelPath, sessionOptions);
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Create inference session using compiled model
Ort::Session session(env, compiledModelPath.c_str(), sessionOptions);
```

### [Python](#tab/python)

```python
# Create inference session using compiled model
session = ort.InferenceSession(output_model_path, sess_options=options)
```

---

We suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/) for more info about how to use the ONNX Runtime APIs within Windows ML.

## See also

* [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md)
* [Select execution providers](./select-execution-providers.md)
* [Run ONNX models](./run-onnx-models.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)