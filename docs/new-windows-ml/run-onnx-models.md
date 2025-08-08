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

### [C#](#tab/csharp)

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

## Compile models

Before using an ONNX model in an inference session, it often must be compiled into an optimized representation that can be executed efficiently on the device's underlying hardware.

As of the 1.22 release, the ONNX Runtime has introduced new APIs to better encapsulate the compilation steps. More details are available in the ONNX Runtime compile documentation (see [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html)).

#### [C#](#tab/csharp)

```csharp
// Prepare compilation options using our session we configured in step 3
OrtModelCompilationOptions compileOptions = new(sessionOptions);
compileOptions.SetInputModelPath(modelPath);
compileOptions.SetOutputModelPath(compiledModelPath);

// Compile the model
compileOptions.CompileModel();
```

#### [C++](#tab/cppwinrt)

```cppwinrt
const OrtCompileApi* compileApi = ortApi.GetCompileApi();

// Prepare compilation options
OrtModelCompilationOptions* compileOptions = nullptr;
OrtStatus* status = compileApi->CreateModelCompilationOptionsFromSessionOption(env, sessionOptions, &compileOptions);
status = compileApi->ModelCompilationOptions_SetInputModelPath(compileOptions, modelPath.c_str());
status = compileApi->ModelCompilationOptions_SetOutputModelPath(compileOptions, compiledModelPath.c_str());

// Compile the model
status = compileApi->CompileModel(env, compileOptions);

// Clean up
compileApi->ReleaseModelCompilationOptions(compileOptions);
```

#### [Python](#tab/python)

```python
input_model_path = "path_to_your_model.onnx"
output_model_path = "path_to_your_compiled_model.onnx"

model_compiler = ort.ModelCompiler(
    options,
    input_model_path,
    embed_compiled_data_into_model=True,
    external_initializers_file_path=None,
)
model_compiler.compile_to_file(output_model_path)
if not os.path.exists(output_model_path):
    # For some EP, there might not be a compilation output.
    # In that case, use the original model directly.
    output_model_path = input_model_path
```

---

> [!NOTE]
> Compilation can take several minutes to complete. So that any UI remains responsive, consider doing this as a background operation in your application.

## See also

* [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md)
* [Initialize execution providers](./initialize-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)