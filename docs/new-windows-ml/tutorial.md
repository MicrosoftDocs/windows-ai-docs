---
title: Use Windows ML to run the ResNet-50 model
description: An outline of the process of running the ResNet-50 model using Windows ML, detailing model acquisition and preprocessing steps.
ms.date: 05/01/2025
ms.topic: article
---

# Use Windows ML to run the ResNet-50 model

This topic outlines the process of running the ResNet-50 model using Windows ML, detailing model acquisition and preprocessing steps. The implementation involves dynamically selecting execution providers for optimized inference performance.
* **About**. The ResNet-50 model is a PyTorch model intended for image classification.
* **Acquiring the model, and preprocessing**. You can acquire the ResNet-50 model from Hugging Face, and convert it to QDQ ONNX format by using the AI Toolkit.
* **Running the inference**. You then load the model, prepare input tensors, and run inference using the Windows ML APIs, including post-processing steps to apply softmax, and retrieve the top predictions.

## Acquiring the model, and preprocessing

You can acquire [ResNet-50](https://huggingface.co/microsoft/resnet-50) from Hugging Face (the platform where the ML community collaborates on models, datasets, and apps). You'll convert ResNet-50 to QDQ ONNX format by using the AI Toolkit (see [What is the AI Toolkit for Visual Studio Code?](/windows/ai/toolkit/)).

The goal of this example code is to leverage the Windows ML runtime to do the heavy lifting.

The Windows ML runtime will:
* Load the model.
* Then dynamically select the IHV-provided execution provider (EP) for the model (selection of the *optimal* EP is currently in active development).
* It then downloads the EP from the Microsoft Store on demand, and starts using the EP.
* And will then run inference on the model.

> [!NOTE]
> The use of the **IOnnxruntimeExecutionProviderNative** is a temporary measure until the EP auto-download feature is completed.

```cpp
struct __declspec(uuid("7c8aced5-6e19-4c42-9579-d07e2ba4fe17")) __declspec(novtable) IOnnxruntimeExecutionProviderNative : IUnknown
{
    STDMETHOD(Register)(const OrtApi* ortApi, OrtSessionOptions* pSessionOptions) PURE;
};

...

void initialize_windowsml_runtime(OrtSessionOptions* sessionOptions, winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure& infrastructure)
{
    // Initialize environment and session.
    const auto& api = Ort::GetApi();
    // Use WinML to update packages, and so on.
    infrastructure.DownloadPackagesAsync().get();

    auto executionProviders = infrastructure.LoadExecutionProvidersAsync().get();

    for (auto ep : executionProviders)
    {
        auto onnxRuntimeExecutionProvider = ep.try_as<IOnnxruntimeExecutionProviderNative>();
        if (onnxRuntimeExecutionProvider)
        {
            // Call methods on onnxRuntimeExecutionProvider in this block.
            // For example, you might call:
            onnxRuntimeExecutionProvider->Register(&api, sessionOptions);
        }
    }
}
```

### EP compilation

Because Windows ML dynamically selects the execution provider (EP), the model needs to be compiled against that EP in order to run fast inferences. This is a one-time process. The example code below handles it by compiling the model on the first run, and then storing it locally. Subsequent runs of the code pick up the compiled version, and run that; resulting in optimized fast inferences.

```cpp
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
    std::cout << "No compiled model found, attempting to create compiled model at " << compiledModelPath << std::endl;

    Ort::ModelCompilationOptions compile_options(env, session_options);
    compile_options.SetInputModelPath(modelPath.c_str());
    compile_options.SetOutputModelPath(compiledModelPath.c_str());

    std::cout << "Starting compile, this may take a few moments..." << std::endl;
    Ort::Status compileStatus = Ort::CompileModel(env, compile_options);
    if (compileStatus.IsOK())
    {
        // Calculate the duration in minutes / seconds / milliseconds.
        std::cout << "Model compiled successfully!" << std::endl;
        isCompiledModelAvailable = std::filesystem::exists(compiledModelPath);
    }
    else
    {
        std::cerr << "Failed to compile model: " << compileStatus.GetErrorCode() << ", " << compileStatus.GetErrorMessage() << std::endl;
        std::cerr << "Falling back to uncompiled model" << std::endl;
    }
}
```

## Running the inference

The input image is converted to tensor data format, and then inference runs on it. While this is typical of all code that uses the ONNX Runtime, the difference in this case is that it's ONNX Runtime directly through Windows ML. The only requirement is adding `#include <win_onnxruntime_cxx_api.h>` to the code.

```cpp
std::filesystem::path modelPathToUse = isCompiledModelAvailable ? compiledModelPath : modelPath;
Ort::Session session(env, modelPathToUse.c_str(), session_options);

// Prepare the input tensor.
winrt::hstring imagePath{ catImagePath.c_str() };
auto imageFrameResult = ResnetModelHelper::LoadImageFileAsync(imagePath);
auto inputTensorData = ResnetModelHelper::BindSoftwareBitmapAsTensor(imageFrameResult.get());
const int64_t inputShape[] = { 1, 3, 224, 224 }; // Batch size, channels, height, width.

Ort::MemoryInfo memoryInfo = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
Ort::Value inputTensor = Ort::Value::CreateTensor<float>(
    memoryInfo, inputTensorData.data(), inputTensorData.size(), inputShape, 4);

// Get input/output names.
Ort::AllocatorWithDefaultOptions allocator;
auto inputName = session.GetInputNameAllocated(0, allocator);
auto outputName = session.GetOutputNameAllocated(0, allocator);
std::vector<const char*> inputNames = { inputName.get() };
std::vector<const char*> outputNames = { outputName.get() };

// Run inference.
auto outputTensors = session.Run(
    Ort::RunOptions{ nullptr }, inputNames.data(), &inputTensor, 1, outputNames.data(), 1);

// Extract results.
float* outputData = outputTensors[0].GetTensorMutableData<float>();
size_t outputSize = outputTensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
std::vector<float> results(outputData, outputData + outputSize);

std::filesystem::path modelPathToUse = isCompiledModelAvailable ? compiledModelPath : modelPath;
Ort::Session session(env, modelPathToUse.c_str(), session_options);

// Prepare the input tensor.
winrt::hstring imagePath{ catImagePath.c_str() };
auto imageFrameResult = ResnetModelHelper::LoadImageFileAsync(imagePath);
auto inputTensorData = ResnetModelHelper::BindSoftwareBitmapAsTensor(imageFrameResult.get());
const int64_t inputShape[] = { 1, 3, 224, 224 }; // Batch size, channels, height, width

Ort::MemoryInfo memoryInfo = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
Ort::Value inputTensor = Ort::Value::CreateTensor<float>(
    memoryInfo, inputTensorData.data(), inputTensorData.size(), inputShape, 4);

// Get input/output names.
Ort::AllocatorWithDefaultOptions allocator;
auto inputName = session.GetInputNameAllocated(0, allocator);
auto outputName = session.GetOutputNameAllocated(0, allocator);
std::vector<const char*> inputNames = { inputName.get() };
std::vector<const char*> outputNames = { outputName.get() };

// Run inference.
auto outputTensors = session.Run(
    Ort::RunOptions{ nullptr }, inputNames.data(), &inputTensor, 1, outputNames.data(), 1);

// Extract results.
float* outputData = outputTensors[0].GetTensorMutableData<float>();
size_t outputSize = outputTensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
std::vector<float> results(outputData, outputData + outputSize);
```

### Post-processing.

The softmax function is applied to returned raw output, and label data is used to map and print the names with the five highest probabilities.

```cpp
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

> [!NOTE]
> The complete code will be available on GitHub after the Microsoft Build 2025 conference.
