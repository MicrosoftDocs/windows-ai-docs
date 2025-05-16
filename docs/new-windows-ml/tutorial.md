---
title: Tutorial and code example
description: An outline of the process of running the ResNet-50 model using Windows ML, detailing model acquisition and preprocessing steps.
ms.date: 05/12/2025
ms.topic: article
---

# Tutorial and code example

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

This topic outlines the process of running the ResNet-50 model using Windows ML, detailing model acquisition and preprocessing steps. The implementation involves dynamically selecting execution providers for optimized inference performance.
* **About**. The ResNet-50 model is a PyTorch model intended for image classification.
* **Acquiring the model, and preprocessing**. You can acquire the ResNet-50 model from Hugging Face, and convert it to QDQ ONNX format by using the AI Toolkit.
* **Running the inference**. You then load the model, prepare input tensors, and run inference using the Windows ML APIs, including post-processing steps to apply softmax, and retrieve the top predictions.

## Acquiring the model, and preprocessing

You can acquire [ResNet-50](https://huggingface.co/microsoft/resnet-50) from Hugging Face (the platform where the ML community collaborates on models, datasets, and apps). You'll convert ResNet-50 to QDQ ONNX format by using the AI Toolkit (see [What is the AI Toolkit for Visual Studio Code?](/windows/ai/toolkit/)).

The goal of this example code is to leverage the Windows ML runtime to do the heavy lifting.

The Windows ML runtime will:
* Load the model.
* Dynamically select the preferred IHV-provided execution provider (EP) for the model and download its EP from the Microsoft Store, on demand.
* Run inference on the model using the EP.

For API reference, see [**OrtCompileApi struct**](https://onnxruntime.ai/docs/api/c/struct_ort_api.html), [**OrtSessionOptions**](https://onnxruntime.ai/docs/api/c/group___global.html#gaa6c56bcb36e39611481a17065d3ce620), [**Microsoft::Windows::AI::MachineLearning::Infrastructure class**](./api-reference.md#infrastructure-class), and [**Ort::GetApi**](https://onnxruntime.ai/docs/api/c/namespace_ort.html#a296b5958479d9889218b17bdb08c1894).

```cppwinrt

winrt::init_apartment();
// Initialize ONNX Runtime
Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "CppConsoleDesktop");

// Use WinML to download and register Execution Providers
WinMLDeployMainPackage();
winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;
infrastructure.DownloadPackagesAsync().get();
infrastructure.RegisterExecutionProviderLibrariesAsync().get();

// Set the auto EP selection policy
Ort::SessionOptions sessionOptions;
sessionOptions.SetEpSelectionPolicy(OrtExecutionProviderDevicePolicy_PREFER_NPU);

```

### EP compilation

Because Windows ML dynamically selects the execution provider (EP), the model needs to be compiled against that EP in order to run fast inferences. This is a one-time process. The example code below handles it by compiling the model on the first run, and then storing it locally. Subsequent runs of the code pick up the compiled version, and run that; resulting in optimized fast inferences.

For API reference, see [**Ort::ModelCompilationOptions struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_model_compilation_options.html), [**Ort::Status struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_status.html), and [**Ort::CompileModel**](https://onnxruntime.ai/docs/api/c/namespace_ort.html#af5ec45452237ac4ab98dd7a11b9d678e).

```cppwinrt
// Prepare paths for model and labels
std::filesystem::path executableFolder = ResnetModelHelper::GetExecutablePath().parent_path();
std::filesystem::path modelPath = executableFolder / "model\\model.onnx";
std::filesystem::path labelsPath = executableFolder / "ResNet50Labels.txt";
std::filesystem::path catImagePath = executableFolder / "cat.jpg";

std::filesystem::path compiledModelPath = executableFolder / "model\\model_ctx.onnx";
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

## Running the inference

The input image is converted to tensor data format, and then inference runs on it. While this is typical of all code that uses the ONNX Runtime, the difference in this case is that it's ONNX Runtime directly through Windows ML. The only requirement is adding `#include <win_onnxruntime_cxx_api.h>` to the code.

Also see [Convert a model with AI Toolkit for VS Code](https://github.com/microsoft/windows-ai-studio-templates/blob/2c1682a47a2f7090dc920cf5e5b7a3bf5d0f91a1/model_lab_configs/docs/topics/QuickStart.md)

For API reference, see [**Ort::Session struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_session.html), [**Ort::MemoryInfo struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_memory_info.html), [**Ort::Value struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_value.html), [**Ort::AllocatorWithDefaultOptions struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_allocator_with_default_options.html), [**Ort::RunOptions struct**](https://onnxruntime.ai/docs/api/c/struct_ort_1_1_run_options.html).

```cppwinrt
std::filesystem::path modelPathToUse = isCompiledModelAvailable ? compiledModelPath : modelPath;

// Create the session and load the model
Ort::Session session(env, modelPathToUse.c_str(), sessionOptions);

// Load and Preprocess image
winrt::hstring imagePath{catImagePath.c_str()};
auto imageFrameResult = ResnetModelHelper::LoadImageFileAsync(imagePath);
auto inputTensorData = ResnetModelHelper::BindSoftwareBitmapAsTensor(imageFrameResult.get());

// Prepare input tensor
const int64_t inputShape[] = {1, 3, 224, 224}; // Batch size, channels, height, width
Ort::MemoryInfo memoryInfo = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
Ort::Value inputTensor =
    Ort::Value::CreateTensor<float>(memoryInfo, inputTensorData.data(), inputTensorData.size(), inputShape, 4);

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
float* outputData = outputTensors[0].GetTensorMutableData<float>();
size_t outputSize = outputTensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
std::vector<float> results(outputData, outputData + outputSize);

// Load labels and print result
auto labels = ResnetModelHelper::LoadLabels(labelsPath);
ResnetModelHelper::PrintResults(labels, results);

inputName.release();
outputName.release();
```

### Post-processing.

The softmax function is applied to returned raw output, and label data is used to map and print the names with the five highest probabilities.

```cppwinrt
// Load labels and print results.
auto labels = ResnetModelHelper::LoadLabels(labelsPath);
ResnetModelHelper::PrintResults(labels, results);
```

### Output  

Here's an example of the kind of output to be expected.

```console
285, Egyptian cat with confidence of 0.904274
281, tabby with confidence of 0.0620204
282, tiger cat with confidence of 0.0223081
287, lynx with confidence of 0.00119624
761, remote control with confidence of 0.000487919
```

## Full code sample

You can [view the full code sample here](https://github.com/microsoft/Build25-BRK225).