---
title: Run ONNX models with Windows ML
description: Learn how to use Windows Machine Learning (ML) to run local AI ONNX models in your Windows apps.
ms.date: 05/20/2025
ms.topic: how-to
---

# Run ONNX models with Windows ML

Windows Machine Learning (ML) enables your apps to use the ONNX Runtime without distributing your own copy of the runtime and the EPs. Your app will depend on Windows ML to dynamically download, update, and initialize EPs that are shared system-wide, and will use the shared copy of the [ONNX Runtime](https://onnxruntime.ai/) that ships with Windows ML!

## Prerequisites

* Follow all the steps in [Get started with Windows ML](./get-started.md).

## ONNX Runtime APIs in Windows ML

Windows ML ships a copy of the ONNX Runtime. That means when you install the Windows ML via Windows App SDK, your app will have access to the full ONNX Runtime API surface.

To see what version of the ONNX Runtime is in a specific version of Windows ML, see the [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md) docs.

The namespaces for the ONNX Runtime APIs within Windows ML are as follows...

```csharp
using Microsoft.ML.OnnxRuntime;
```

### [C++](#tab/cppwinrt)

```cppwinrt
#include <win_onnxruntime_cxx_api.h>
```


### [Python](#tab/python)

```python
import onnxruntime as ort
```

---

The APIs are all the same as ONNX Runtime, for example, creating an inference session...

#### [C#](#tab/csharp)

```csharp
// Create inference session using compiled model
using InferenceSession session = new(compiledModelPath, sessionOptions);
```

#### [C++](#tab/cppwinrt)

```cppwinrt
// Create inference session using compiled model
Ort::Session session(env, compiledModelPath.c_str(), sessionOptions);
```

#### [Python](#tab/python)

```python
# Create inference session using compiled model
session = ort.InferenceSession(output_model_path, sess_options=options)
```

---

We suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/) for more info about how to use the ONNX Runtime APIs within Windows ML.

## See also

* [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md)
* [Initialize execution providers](./initialize-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)