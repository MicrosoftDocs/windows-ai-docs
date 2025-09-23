---
title: Run ONNX models using the ONNX Runtime included in Windows ML
description: Learn how to use Windows Machine Learning (ML) to run local AI ONNX models in your Windows apps.
ms.date: 05/20/2025
ms.topic: how-to
---

# Run ONNX models using the ONNX Runtime included in Windows ML

The ONNX Runtime shipped with Windows ML allows apps to run inference on ONNX models locally.

## Creating an inference session

The APIs are the same as when using ONNX Runtime directly. For example, to create an inference session:

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
import onnxruntime as ort
# Create inference session using compiled model
session = ort.InferenceSession(output_model_path, sess_options=options)
```

---

We suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/) for more info about how to use the ONNX Runtime APIs within Windows ML. Model inference code will be different for every model.

## Compile models

Before using an ONNX model in an inference session, it often must be compiled into an optimized representation that can be executed efficiently on the device's underlying hardware.

As of ONNX Runtime 1.22, there are new APIs that better encapsulate the compilation steps. More details are available in the ONNX Runtime compile documentation (see [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html)).

#### [C#](#tab/csharp)

```csharp
// Prepare compilation options
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
OrtStatus* status = compileApi->CreateModelCompilationOptionsFromSessionOptions(env, sessionOptions, &compileOptions);
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

> [!TIP]
> For optimal performance, compile your models once, and reuse the compiled version. Store compiled models in your app's local data folder for subsequent runs. Note that updates to the EPs or runtime might require recompiling.

## See also

* [Use ONNX APIs in Windows ML](./use-onnx-apis.md)
* [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md)
* [Initialize execution providers](./initialize-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)
