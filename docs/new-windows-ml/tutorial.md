---
title: Windows ML walkthrough
description: An outline of the process of running the ResNet-50 model using Windows ML, detailing model acquisition and preprocessing steps.
ms.date: 07/07/2025
ms.topic: article
---

# Windows ML walkthrough

This short tutorial walks through using Windows ML to run the ResNet-50 image classification model on Windows, detailing model acquisition and preprocessing steps. The implementation involves dynamically selecting execution providers for optimized inference performance.

The ResNet-50 model is a PyTorch model intended for image classification.

In this tutorial, you'll acquire the ResNet-50 model from Hugging Face, and convert it to QDQ ONNX format by using the AI Toolkit.

Then you'll load the model, prepare input tensors, and run inference using the Windows ML APIs, including post-processing steps to apply softmax, and retrieve the top predictions.

## Acquiring the model, and preprocessing

You can acquire [ResNet-50](https://huggingface.co/microsoft/resnet-50) from Hugging Face (the platform where the ML community collaborates on models, datasets, and apps). You'll convert ResNet-50 to QDQ ONNX format by using the AI Toolkit (see [convert models to ONNX format](https://code.visualstudio.com/docs/intelligentapps/modelconversion) for more info).

The goal of this example code is to leverage the Windows ML runtime to do the heavy lifting.

The Windows ML runtime will:

* Load the model.
* Dynamically select the preferred IHV-provided execution provider (EP) for the model and download its EP from the Microsoft Store, on demand.
* Run inference on the model using the EP.

For API reference, see [**OrtSessionOptions**](https://onnxruntime.ai/docs/api/c/group___global.html#gaa6c56bcb36e39611481a17065d3ce620) and [**Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog class**](./api-reference.md#executionprovidercatalog-class).

### [C#](#tab/csharp)

```csharp
// Create a new instance of EnvironmentCreationOptions
EnvironmentCreationOptions envOptions = new()
{
    logId = "ResnetDemo",
    logLevel = OrtLoggingLevel.ORT_LOGGING_LEVEL_ERROR
};

// Pass the options by reference to CreateInstanceWithOptions
OrtEnv ortEnv = OrtEnv.CreateInstanceWithOptions(ref envOptions);

// Use Windows ML to download and register Execution Providers
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();
Console.WriteLine("Ensuring and registering execution providers...");
await catalog.EnsureAndRegisterCertifiedAsync();

//Create Onnx session
Console.WriteLine("Creating session ...");
var sessionOptions = new SessionOptions();
// Set EP Selection Policy
sessionOptions.SetEpSelectionPolicy(ExecutionProviderDevicePolicy.MIN_OVERALL_POWER);
```

### [C++](#tab/cpp)

```cpp
winrt::init_apartment();
// Initialize ONNX Runtime
Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "CppConsoleDesktop");

// Use Windows ML to download and register Execution Providers
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
catalog.EnsureAndRegisterCertifiedAsync().get();

// Set the auto EP selection policy
Ort::SessionOptions sessionOptions;
sessionOptions.SetEpSelectionPolicy(OrtExecutionProviderDevicePolicy_MIN_OVERALL_POWER);
```

### [Python](#tab/python)

```python
# In your application code
import subprocess
import json
import sys
from pathlib import Path
import traceback
import onnxruntime as ort

_winml_instance = None

class WinML:
    def __new__(cls, *args, **kwargs):
        global _winml_instance
        if _winml_instance is None:
            _winml_instance = super(WinML, cls).__new__(cls, *args, **kwargs)
            _winml_instance._initialized = False
        return _winml_instance

    def __init__(self):
        if self._initialized:
            return
        self._initialized = True

        self._fix_winrt_runtime()
        from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
            InitializeOptions,
            initialize
        )
        import winui3.microsoft.windows.ai.machinelearning as winml
        self._win_app_sdk_handle = initialize(options=InitializeOptions.ON_NO_MATCH_SHOW_UI)
        self._win_app_sdk_handle.__enter__()
        catalog = winml.ExecutionProviderCatalog.get_default()
        self._providers = catalog.find_all_providers()
        self._ep_paths : dict[str, str] = {}
        for provider in self._providers:
            provider.ensure_ready_async().get()
            if provider.library_path == '':
                continue
            self._ep_paths[provider.name] = provider.library_path
        self._registered_eps : list[str] = []

    def __del__(self):
        self._providers = None
        self._win_app_sdk_handle.__exit__(None, None, None)

    def _fix_winrt_runtime(self):
        """
        This function removes the msvcp140.dll from the winrt-runtime package.
        So it does not cause issues with other libraries.
        """
        from importlib import metadata
        site_packages_path = Path(str(metadata.distribution('winrt-runtime').locate_file('')))
        dll_path = site_packages_path / 'winrt' / 'msvcp140.dll'
        if dll_path.exists():
            dll_path.unlink()

    def register_execution_providers_to_ort(self) -> list[str]:
        import onnxruntime as ort
        for name, path in self._ep_paths.items():
            if name not in self._registered_eps:
                try:
                    ort.register_execution_provider_library(name, path)
                    self._registered_eps.append(name)
                except Exception as e:
                    print(f"Failed to register execution provider {name}: {e}", file=sys.stderr)
                    traceback.print_exc()
        return self._registered_eps

WinML().register_execution_providers_to_ort()

session_options = ort.SessionOptions()
session_options.set_provider_selection_policy(ort.OrtExecutionProviderDevicePolicy.MAX_EFFICIENCY)
```

---

## EP compilation

If your model isn't already compiled for the EP (which may vary depending on device), the model first needs to be compiled against that EP. This is a one-time process. The example code below handles it by compiling the model on the first run, and then storing it locally. Subsequent runs of the code pick up the compiled version, and run that; resulting in optimized fast inferences.

For API reference, see [**Ort::ModelCompilationOptions struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_model_compilation_options.html), [**Ort::Status struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_status.html), and [**Ort::CompileModel**](https://onnxruntime.ai/docs/api/c/namespace_ort.html#af5ec45452237ac4ab98dd7a11b9d678e).

### [C#](#tab/csharp)

```csharp
// Prepare paths
string executableFolder = Path.GetDirectoryName(Assembly.GetEntryAssembly()!.Location)!;
string labelsPath = Path.Combine(executableFolder, "ResNet50Labels.txt");
string imagePath = Path.Combine(executableFolder, "dog.jpg");
            
// TODO: Please use AITK Model Conversion tool to download and convert Resnet, and paste the converted path here
string modelPath = @"";
string compiledModelPath = @"";

// Compile the model if not already compiled
bool isCompiled = File.Exists(compiledModelPath);
if (!isCompiled)
{
    Console.WriteLine("No compiled model found. Compiling model ...");
    using (var compileOptions = new OrtModelCompilationOptions(sessionOptions))
    {
        compileOptions.SetInputModelPath(modelPath);
        compileOptions.SetOutputModelPath(compiledModelPath);
        compileOptions.CompileModel();
        isCompiled = File.Exists(compiledModelPath);
        if (isCompiled)
        {
            Console.WriteLine("Model compiled successfully!");
        }
        else
        {
            Console.WriteLine("Failed to compile the model. Will use original model.");
        }
    }
}
else
{
    Console.WriteLine("Found precompiled model.");
}
var modelPathToUse = isCompiled ? compiledModelPath : modelPath;
```

### [C++](#tab/cpp)

```cpp
// Prepare paths for model and labels
std::filesystem::path executableFolder = ResnetModelHelper::GetExecutablePath().parent_path();
std::filesystem::path labelsPath = executableFolder / "ResNet50Labels.txt";
std::filesystem::path dogImagePath = executableFolder / "dog.jpg";

// TODO: use AITK Model Conversion tool to get resnet and paste the path here
std::filesystem::path modelPath = L"";
std::filesystem::path compiledModelPath = L"";
bool isCompiledModelAvailable = std::filesystem::exists(compiledModelPath);

if (isCompiledModelAvailable)
{
    std::cout << "Using compiled model: " << compiledModelPath << std::endl;
}
else
{
    std::cout << "No compiled model found, attempting to create compiled model at " << compiledModelPath
                << std::endl;

    Ort::ModelCompilationOptions compile_options(env, sessionOptions);
    compile_options.SetInputModelPath(modelPath.c_str());
    compile_options.SetOutputModelPath(compiledModelPath.c_str());

    std::cout << "Starting compile, this may take a few moments..." << std::endl;
    Ort::Status compileStatus = Ort::CompileModel(env, compile_options);
    if (compileStatus.IsOK())
    {
        // Calculate the duration in minutes / seconds / milliseconds
        std::cout << "Model compiled successfully!" << std::endl;
        isCompiledModelAvailable = std::filesystem::exists(compiledModelPath);
    }
    else
    {
        std::cerr << "Failed to compile model: " << compileStatus.GetErrorCode() << ", "
                    << compileStatus.GetErrorMessage() << std::endl;
        std::cerr << "Falling back to uncompiled model" << std::endl;
    }
}
std::filesystem::path modelPathToUse = isCompiledModelAvailable ? compiledModelPath : modelPath;
```

### [Python](#tab/python)

```python
model_path = "path to your original model"
compiled_model_path = "path to your compiled model"

if compiled_model_path.exists():
    print("Using compiled model")
else:
    print("No compiled model found, attempting to create compiled model at ", compiled_model_path)  
    model_compiler = ort.ModelCompiler(session_options, model_path)
    print("Starting compile, this may take a few moments..." )
    try:
        model_compiler.compile_to_file(compiled_model_path)
        print("Model compiled successfully")
    except Exception as e:
        print("Model compilation failed:", e)
        print("Falling back to uncompiled model")

model_path_to_use = compiled_model_path if compiled_model_path.exists() else model_path
```

---

## Running the inference

The input image is converted to tensor data format, and then inference runs on it. While this is typical of all code that uses the ONNX Runtime, the difference in this case is that it's ONNX Runtime directly through Windows ML. The only requirement is adding `#include <winml/onnxruntime_cxx_api.h>` to the code.

Also see [Convert a model with AI Toolkit for VS Code](https://code.visualstudio.com/docs/intelligentapps/modelconversion)

For API reference, see [**Ort::Session struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_session.html), [**Ort::MemoryInfo struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_memory_info.html), [**Ort::Value struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_value.html), [**Ort::AllocatorWithDefaultOptions struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_allocator_with_default_options.html), [**Ort::RunOptions struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_run_options.html).

### [C#](#tab/csharp)

```csharp
using var session = new InferenceSession(modelPathToUse, sessionOptions);

Console.WriteLine("Preparing input ...");
// Load and preprocess image
var input = await PreprocessImageAsync(await LoadImageFileAsync(imagePath));
// Prepare input tensor
var inputName = session.InputMetadata.First().Key;
var inputTensor = new DenseTensor<float>(
    input.ToArray(),          // Use the DenseTensor<float> directly
    new[] { 1, 3, 224, 224 }, // Shape of the tensor
    false                     // isReversedStride should be explicitly set to false
);

// Bind inputs and run inference
var inputs = new List<NamedOnnxValue>
{
    NamedOnnxValue.CreateFromTensor(inputName, inputTensor)
};

Console.WriteLine("Running inference ...");
var results = session.Run(inputs);
for (int i = 0; i < 40; i++)
{
    results = session.Run(inputs);
}

// Extract output tensor
var outputName = session.OutputMetadata.First().Key;
var resultTensor = results.First(r => r.Name == outputName).AsEnumerable<float>().ToArray();

// Load labels and print results
var labels = LoadLabels(labelsPath);
PrintResults(labels, resultTensor);
```

### [C++](#tab/cpp)

```cpp
Ort::Session session(env, modelPathToUse.c_str(), sessionOptions);
std::cout << "ResNet model loaded"<< std::endl;

// Load and Preprocess image
winrt::hstring imagePath{ dogImagePath.c_str()};
auto imageFrameResult = ResnetModelHelper::LoadImageFileAsync(imagePath);
auto inputTensorData = ResnetModelHelper::BindSoftwareBitmapAsTensor(imageFrameResult.get());

// Prepare input tensor
auto inputInfo = session.GetInputTypeInfo(0).GetTensorTypeAndShapeInfo();
auto inputType = inputInfo.GetElementType();

auto inputShape = std::array<int64_t, 4>{ 1, 3, 224, 224 };
auto memoryInfo = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
std::vector<uint8_t> rawInputBytes;

if (inputType == ONNX_TENSOR_ELEMENT_DATA_TYPE_FLOAT16)
{
    auto converted = ResnetModelHelper::ConvertFloat32ToFloat16(inputTensorData);
    rawInputBytes.assign(reinterpret_cast<uint8_t*>(converted.data()),
        reinterpret_cast<uint8_t*>(converted.data()) + converted.size() * sizeof(uint16_t));
}
else
{
    rawInputBytes.assign(reinterpret_cast<uint8_t*>(inputTensorData.data()),
        reinterpret_cast<uint8_t*>(inputTensorData.data()) +
        inputTensorData.size() * sizeof(float));
}

OrtValue* ortValue = nullptr;

Ort::ThrowOnError(Ort::GetApi().CreateTensorWithDataAsOrtValue(memoryInfo, rawInputBytes.data(),
    rawInputBytes.size(), inputShape.data(),
    inputShape.size(), inputType, &ortValue));
Ort::Value inputTensor{ ortValue };

const int iterations = 20;
std::cout << "Running inference for " << iterations << " iterations" << std::endl;
auto before = std::chrono::high_resolution_clock::now();
for (int i = 0; i < iterations; i++)
{
    //std::cout << "---------------------------------------------" << std::endl;
    //std::cout << "Running inference for " << i + 1 << "th time" << std::endl;
    //std::cout << "---------------------------------------------"<< std::endl;
    std::cout << ".";
    
    // Get input/output names
    Ort::AllocatorWithDefaultOptions allocator;
    auto inputName = session.GetInputNameAllocated(0, allocator);
    auto outputName = session.GetOutputNameAllocated(0, allocator);
    std::vector<const char*> inputNames = {inputName.get()};
    std::vector<const char*> outputNames = {outputName.get()};

    // Run inference
    auto outputTensors =
        session.Run(Ort::RunOptions{nullptr}, inputNames.data(), &inputTensor, 1, outputNames.data(), 1);

    // Extract results
    std::vector<float> results;
    if (inputType == ONNX_TENSOR_ELEMENT_DATA_TYPE_FLOAT16)
    {
        auto outputData = outputTensors[0].GetTensorMutableData<uint16_t>();
        size_t outputSize = outputTensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
        std::vector<uint16_t> outputFloat16(outputData, outputData + outputSize);
        results = ResnetModelHelper::ConvertFloat16ToFloat32(outputFloat16);
    }
    else
    {
        auto outputData = outputTensors[0].GetTensorMutableData<float>();
        size_t outputSize = outputTensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
        results.assign(outputData, outputData + outputSize);
    }

    if (i == iterations - 1)
    {
        // Load labels and print result
        std::cout << "\nOutput for the last iteration"<< std::endl;
        auto labels = ResnetModelHelper::LoadLabels(labelsPath);
        ResnetModelHelper::PrintResults(labels, results);
    }
    inputName.release();
    outputName.release();
}
std::cout << "---------------------------------------------" << std::endl;

```

### [Python](#tab/python)

```python
def load_labels(label_file):
    with open(label_file, 'r') as f:
        labels = [line.strip().split(',')[1] for line in f.readlines()]
    return labels

def load_and_preprocess_image(image_path):
    img = Image.open(image_path)    
    if img.mode != 'RGB':
        img = img.convert('RGB')
    img = img.resize((224, 224))
    means = np.array([0.485, 0.456, 0.406]).reshape(1, 1, 3)
    stds = np.array([0.229, 0.224, 0.225]).reshape(1, 1, 3)
    img_array = np.array(img).astype(np.float32)
    img_array = (img_array - means) / stds    
    img_array = img_array.transpose((2, 0, 1))
    img_array = np.expand_dims(img_array, axis=0)
    return img_array.astype(np.float32)

session = ort.InferenceSession(
    model_path_to_use,
    sess_options=session_options,
)

labels = load_labels("path to your labels file")

images_folder = "path to your images' folder"
for image_file in images_folder.iterdir():  
    print(f"Running inference on image: {image_file}")
    print("Preparing input ...")
    img_array = load_and_preprocess_image(image_file)
    print("Running inference ...")
    input_name = session.get_inputs()[0].name
    results = session.run(None, {input_name: img_array})[0]
    # See the next section for this function's definition
    print_results(labels, results, is_logit=False)
```

---

## Post-processing

The softmax function is applied to returned raw output, and label data is used to map and print the names with the five highest probabilities.

### [C#](#tab/csharp)

```csharp
private static void PrintResults(IList<string> labels, IReadOnlyList<float> results)
{
    // Apply softmax to the results
    float maxLogit = results.Max();
    var expScores = results.Select(r => MathF.Exp(r - maxLogit)).ToList(); // stability with maxLogit
    float sumExp = expScores.Sum();
    var softmaxResults = expScores.Select(e => e / sumExp).ToList();

    // Get top 5 results
    IEnumerable<(int Index, float Confidence)> topResults = softmaxResults
        .Select((value, index) => (Index: index, Confidence: value))
        .OrderByDescending(x => x.Confidence)
        .Take(5);

    // Display results
    Console.WriteLine("Top Predictions:");
    Console.WriteLine("-------------------------------------------");
    Console.WriteLine("{0,-32} {1,10}", "Label", "Confidence");
    Console.WriteLine("-------------------------------------------");

    foreach (var result in topResults)
    {
        Console.WriteLine("{0,-32} {1,10:P2}", labels[result.Index], result.Confidence);
    }

    Console.WriteLine("-------------------------------------------");
}
```

### [C++](#tab/cpp)

```cpp
void PrintResults(const std::vector<std::string>& labels, const std::vector<float>& results) {
    // Apply softmax to the results  
    float maxLogit = *std::max_element(results.begin(), results.end());
    std::vector<float> expScores;
    float sumExp = 0.0f;

    for (float r : results) {
        float expScore = std::exp(r - maxLogit);
        expScores.push_back(expScore);
        sumExp += expScore;
    }

    std::vector<float> softmaxResults;
    for (float e : expScores) {
        softmaxResults.push_back(e / sumExp);
    }

    // Get top 5 results  
    std::vector<std::pair<int, float>> indexedResults;
    for (size_t i = 0; i < softmaxResults.size(); ++i) {
        indexedResults.emplace_back(static_cast<int>(i), softmaxResults[i]);
    }

    std::sort(indexedResults.begin(), indexedResults.end(), [](const auto& a, const auto& b) {
        return a.second > b.second;
        });

    indexedResults.resize(std::min<size_t>(5, indexedResults.size()));

    // Display results  
    std::cout << "Top Predictions:\n";
    std::cout << "-------------------------------------------\n";
    std::cout << std::left << std::setw(32) << "Label" << std::right << std::setw(10) << "Confidence\n";
    std::cout << "-------------------------------------------\n";

    for (const auto& result : indexedResults) {
        std::cout << std::left << std::setw(32) << labels[result.first]
            << std::right << std::setw(10) << std::fixed << std::setprecision(2) << (result.second * 100) << "%\n";
    }

    std::cout << "-------------------------------------------\n";
}

```

### [Python](#tab/python)

```python
def print_results(labels, results, is_logit=False):
    def softmax(x):
        exp_x = np.exp(x - np.max(x))
        return exp_x / exp_x.sum()
    results = results.flatten()
    if is_logit:
        results = softmax(results)
    top_k = 5
    top_indices = np.argsort(results)[-top_k:][::-1]
    print("Top Predictions:")
    print("-"*50)
    print(f"{'Label':<32} {'Confidence':>10}")
    print("-"*50)
    
    for i in top_indices:
        print(f"{labels[i]:<32} {results[i]*100:>10.2f}%")

    print("-"*50)
```

---

### Output  

Here's an example of the kind of output to be expected.

```console
285, Egyptian cat with confidence of 0.904274
281, tabby with confidence of 0.0620204
282, tiger cat with confidence of 0.0223081
287, lynx with confidence of 0.00119624
761, remote control with confidence of 0.000487919
```

## Full code samples

The full code samples are available in the WindowsAppSDK-Samples GitHub repository. See [WindowsML](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML).
