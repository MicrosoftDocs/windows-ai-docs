---
title: Using Windows ML to run the ResNet 50 model
description: An outline of the process of running the ResNet 50 model using Windows ML, detailing model acquisition and preprocessing steps.
ms.date: 05/01/2025
ms.topic: article
---

# Using Windows ML to run the ResNet 50 model

This topic outlines the process of running the ResNet 50 model using Windows ML, detailing model acquisition and preprocessing steps. The implementation involves dynamically selecting execution providers for optimized inference performance.
* **About**. The ResNet 50 model is intended for image classification.
* **Acquiring the model, and preprocessing**. You can acquire the ResNet 50 model from Hugging Face, and convert it to QDQ ONNX format by using the AI Toolkit.
* **Running the inference**. You then load the model, prepare input tensors, and run inference using the Windows ML APIs, including post-processing steps to apply softmax, and retrieve the top predictions.

## Acquiring the model, and preprocessing

Fetch resnet 50 from hugging face, process it as onnx and qdq using AITK.
We got Resnet 50 (microsoft/resnet-50 · Hugging Face) model from Hugging Face. It is a PyTorch model which is converted to QDQ Onnx model using AI Toolkit (Overview for the AI Toolkit for Visual Studio Code | Microsoft Learn). 
It is an image classification model.
Actual sample : 
Goal : The sample leveraging WinML runtime, loads the model, dynamically selects the Execution Provider (EP) for it, downloads it from the store (TODO : check if it is ready or not) and starts using it (auto selecting best Execution Provider is currently in active development and not yet ready), and runs inference on the model.

(This IOnnxruntimeExecutionProviderNative will not be needed once auto download of EP feature is completed)
struct __declspec(uuid("7c8aced5-6e19-4c42-9579-d07e2ba4fe17")) __declspec(novtable) IOnnxruntimeExecutionProviderNative : IUnknown
{
    STDMETHOD(Register)(const OrtApi* ortApi, OrtSessionOptions* pSessionOptions) PURE;
};


…

void initialize_windowsml_runtime(OrtSessionOptions* sessionOptions, winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure& infrastructure)
{
    // Initialize environment and session
    const auto& api = Ort::GetApi();
    // Use WinML to update packages, etc...
    infrastructure.DownloadPackagesAsync().get();

    auto executionProviders = infrastructure.LoadExecutionProvidersAsync().get();

    for (auto ep : executionProviders)
    {
        auto onnxRuntimeExecutionProvider = ep.try_as<IOnnxruntimeExecutionProviderNative>();
        if (onnxRuntimeExecutionProvider)
        {
            //    // Call methods on onnxRuntimeExecutionProvider here.
            //    // For example, you might call:
            onnxRuntimeExecutionProvider->Register(&api, sessionOptions);
        }
    }
}


EP Compilation
Since WinML dynamically selects the EP, the model needs to be compiled against that EP to run fast inferences. It is 1 time process only. This code handles that by compiling it on the first run,  and stores it locally. The subsequent runs of the code picks up the compiled version and runs, resulting in optimized fast inferences.

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
            // Calculate the duration in minutes / seconds / milliseconds
            std::cout << "Model compiled successfully!" << std::endl;
            isCompiledModelAvailable = std::filesystem::exists(compiledModelPath);
        }
        else
        {
            std::cerr << "Failed to compile model: " << compileStatus.GetErrorCode() << ", " << compileStatus.GetErrorMessage() << std::endl;
            std::cerr << "Falling back to uncompiled model" << std::endl;
        }
    }




## Running the inference
The input image is converted to tensor data format and inference runs on it. This is atypical code which every onnxruntime code uses. The difference here is it is using Onnxruntime apis directly through WinML. The only requirement is adding #include <win_onnxruntime_cxx_api.h> to the code.

    std::filesystem::path modelPathToUse = isCompiledModelAvailable ? compiledModelPath : modelPath;
    Ort::Session session(env, modelPathToUse.c_str(), session_options);

    // Prepare input tensor
    winrt::hstring imagePath{ catImagePath.c_str() };
    auto imageFrameResult = ResnetModelHelper::LoadImageFileAsync(imagePath);
    auto inputTensorData = ResnetModelHelper::BindSoftwareBitmapAsTensor(imageFrameResult.get());
    const int64_t inputShape[] = { 1, 3, 224, 224 }; // Batch size, channels, height, width

    Ort::MemoryInfo memoryInfo = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
    Ort::Value inputTensor = Ort::Value::CreateTensor<float>(
        memoryInfo, inputTensorData.data(), inputTensorData.size(), inputShape, 4);

    // Get input/output names
    Ort::AllocatorWithDefaultOptions allocator;
    auto inputName = session.GetInputNameAllocated(0, allocator);
    auto outputName = session.GetOutputNameAllocated(0, allocator);
    std::vector<const char*> inputNames = { inputName.get() };
    std::vector<const char*> outputNames = { outputName.get() };

    // Run inference
    auto outputTensors = session.Run(
        Ort::RunOptions{ nullptr }, inputNames.data(), &inputTensor, 1, outputNames.data(), 1);

    // Extract results
    float* outputData = outputTensors[0].GetTensorMutableData<float>();
    size_t outputSize = outputTensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
    std::vector<float> results(outputData, outputData + outputSize);

    std::filesystem::path modelPathToUse = isCompiledModelAvailable ? compiledModelPath : modelPath;
    Ort::Session session(env, modelPathToUse.c_str(), session_options);

    // Prepare input tensor
    winrt::hstring imagePath{ catImagePath.c_str() };
    auto imageFrameResult = ResnetModelHelper::LoadImageFileAsync(imagePath);
    auto inputTensorData = ResnetModelHelper::BindSoftwareBitmapAsTensor(imageFrameResult.get());
    const int64_t inputShape[] = { 1, 3, 224, 224 }; // Batch size, channels, height, width

    Ort::MemoryInfo memoryInfo = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
    Ort::Value inputTensor = Ort::Value::CreateTensor<float>(
        memoryInfo, inputTensorData.data(), inputTensorData.size(), inputShape, 4);

    // Get input/output names
    Ort::AllocatorWithDefaultOptions allocator;
    auto inputName = session.GetInputNameAllocated(0, allocator);
    auto outputName = session.GetOutputNameAllocated(0, allocator);
    std::vector<const char*> inputNames = { inputName.get() };
    std::vector<const char*> outputNames = { outputName.get() };

    // Run inference
    auto outputTensors = session.Run(
        Ort::RunOptions{ nullptr }, inputNames.data(), &inputTensor, 1, outputNames.data(), 1);

    // Extract results
    float* outputData = outputTensors[0].GetTensorMutableData<float>();
    size_t outputSize = outputTensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
    std::vector<float> results(outputData, outputData + outputSize);



Post Processing
Softmax function is applied to returned raw output and label data is using to map and print top 5 names with highest probabilities. 
   // Load labels and print result
    auto labels = ResnetModelHelper::LoadLabels(labelsPath);
    ResnetModelHelper::PrintResults(labels, results);


Output  



The complete code will be able at github at: <TODO> after //Build


 
