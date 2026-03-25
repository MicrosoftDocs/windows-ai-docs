---
title: Run ONNX models using the ONNX Runtime included in Windows ML
description: Learn how to use Windows Machine Learning (ML) to run local AI ONNX models in your Windows apps.
ms.date: 02/11/2026
ms.topic: how-to
---

# Run ONNX models using the ONNX Runtime included in Windows ML

The ONNX Runtime shipped with Windows ML allows apps to run inference on ONNX models locally.

If you're using Generative AI models like Large Language Models (LLMs) and speech-to-text, see [Run LLMs and other generative models](./run-genai-onnx-models.md).

## Create an inference session

The APIs are the same as when using ONNX Runtime directly. For example, to create an inference session:

### [C#](#tab/csharp)

```csharp
// Create inference session using compiled model
using InferenceSession session = new(compiledModelPath, sessionOptions);
```

### [C++](#tab/cpp)

```cpp
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

## Thread spinning behavior

By default, the ONNX Runtime in Windows ML disables [thread spinning](https://onnxruntime.ai/docs/performance/tune-performance/threading.html#thread-spinning-behavior), which typically results in better battery life.

You can enable thread spinning by setting the `"session.intra_op.allow_spinning"` and `"session.inter_op.allow_spinning"` session config entries to `"1"`. We recommend testing your app with and without thread spinning to determine which settings produce the best performance and battery life for your model, use case, and customers.

### [C#](#tab/csharp)

```csharp
// Create session options and enable thread spinning
var sessionOptions = new SessionOptions();
sessionOptions.AddSessionConfigEntry("session.intra_op.allow_spinning", "1");
sessionOptions.AddSessionConfigEntry("session.inter_op.allow_spinning", "1");

// Create inference session using our session options
using InferenceSession session = new(modelPath, sessionOptions);
```

### [C++](#tab/cpp)

```cpp
// Create session options and enable thread spinning
Ort::SessionOptions sessionOptions;
sessionOptions.AddSessionConfigEntry("session.intra_op.allow_spinning", "1");
sessionOptions.AddSessionConfigEntry("session.inter_op.allow_spinning", "1");

// Create inference session using our session options
Ort::Session session(env, modelPath.c_str(), sessionOptions);
```

### [Python](#tab/python)

```python
import onnxruntime as ort

# Create session options and enable thread spinning
options = ort.SessionOptions()
options.AddConfigEntry("session.intra_op.allow_spinning", "1")
options.AddConfigEntry("session.inter_op.allow_spinning", "1")

# Create inference session using our session options
session = ort.InferenceSession(model_path, sess_options=options)
```

---

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

#### [C++](#tab/cpp)

```cpp
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

* [Run GenAI ONNX models](./run-genai-onnx-models.md)
* [Use ONNX APIs in Windows ML](./use-onnx-apis.md)
* [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md)
* [Install execution providers](./initialize-execution-providers.md)
* [Register execution providers](./register-execution-providers.md)
* [Select execution providers](./select-execution-providers.md)
* [Distribute your app that uses Windows ML](./distributing-your-app.md)
