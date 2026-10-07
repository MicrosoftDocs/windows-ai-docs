---
title: Run a model with the Windows ML Runtime API
description: Load a model, build a pipeline, bind a tensor, run inference, and read output with the experimental Windows ML Runtime API.
ms.date: 09/22/2026
ms.topic: tutorial
dev_langs:
- cpp
---

# Run a model with the Windows ML Runtime API

> [!IMPORTANT]
> The Windows ML Runtime APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

This tutorial shows the standard Runtime API sequence without prescribing how
your app acquires or packages its model. The model can be anywhere your app can
read; pass an absolute or relative path to `LoadModelFromFile`.

The helper below intentionally assumes a model with one fixed-shape `FLOAT32`
input. That keeps the tensor-allocation code focused on the Runtime API and is
not a product limitation. If your app uses free dimensions, bind concrete
tensor dimensions for each run; if it uses other data types, create matching
input storage.

## Before you begin

Complete [Set up a C++ project for the Windows ML Runtime API](./get-started.md).
You also need an ONNX model whose input contract you understand.

## Add a model-running helper

Add these headers and the `RunModel` helper to your project:

```cpp
// Copyright (C) Microsoft Corporation. All rights reserved.

#include <Windows.h>
#include <cstdint>
#include <iostream>
#include <string>
#include <vector>
#include <winrt/base.h>

#include <WinMLRuntime.h>

#pragma comment(lib, "windowsapp.lib")

void RunModel(std::wstring const& modelPath)
{
    winrt::com_ptr<IWinMLRuntime> runtime;
    winrt::check_hresult(WinMLCreateRuntime(
        __uuidof(IWinMLRuntime),
        runtime.put_void()));

    winrt::com_ptr<IWinMLModel> model;
    winrt::check_hresult(runtime->LoadModelFromFile(
        modelPath.c_str(),
        nullptr,
        nullptr,
        model.put()));

    winrt::com_ptr<IWinMLExecutionTarget> target;
    winrt::check_hresult(runtime->CreateCpuExecutionTarget(target.put()));

    winrt::com_ptr<IWinMLPipelineBuilder> builder;
    winrt::check_hresult(runtime->CreatePipelineBuilder(builder.put()));

    winrt::com_ptr<IWinMLStage> stage;
    winrt::check_hresult(builder->AddModelStage(
        model.get(),
        target.get(),
        L"model",
        stage.put()));

    winrt::com_ptr<IWinMLPipeline> pipeline;
    winrt::check_hresult(builder->Build(pipeline.put()));

    winrt::com_ptr<IWinMLModelSchema> schema;
    winrt::check_hresult(model->QueryInterface(
        __uuidof(IWinMLModelSchema),
        schema.put_void()));

    WINML_TENSOR_SCHEMA_DESC inputSchema = {};
    winrt::check_hresult(schema->GetInputTensorDesc(0, &inputSchema));

    if (inputSchema.dataType != WINML_TENSOR_DATA_TYPE_FLOAT32 ||
        (inputSchema.dimensionCount > 0 &&
         inputSchema.dimensions == nullptr))
    {
        winrt::throw_hresult(E_INVALIDARG);
    }

    UINT64 elementCount = 1;
    for (UINT32 i = 0; i < inputSchema.dimensionCount; ++i)
    {
        const UINT64 dimension = inputSchema.dimensions[i];
        if (dimension == 0 ||
            dimension == UINT64_MAX ||
            elementCount > UINT64_MAX / dimension)
        {
            winrt::throw_hresult(E_INVALIDARG);
        }

        elementCount *= dimension;
    }

    if (elementCount > SIZE_MAX)
    {
        winrt::throw_hresult(E_BOUNDS);
    }

    std::vector<float> inputData(
        static_cast<size_t>(elementCount),
        0.5f);

    WINML_TENSOR_DESC inputDesc = {};
    inputDesc.dataType = inputSchema.dataType;
    inputDesc.dimensionCount = inputSchema.dimensionCount;
    inputDesc.dimensions = inputSchema.dimensions;

    winrt::com_ptr<IWinMLRawTensorFactory> tensorFactory;
    winrt::check_hresult(target->QueryInterface(
        __uuidof(IWinMLRawTensorFactory),
        tensorFactory.put_void()));

    winrt::com_ptr<IWinMLTensor> inputTensor;
    winrt::check_hresult(tensorFactory->CreateTensor(
        &inputDesc,
        inputData.data(),
        inputData.size() * sizeof(float),
        inputTensor.put()));

    winrt::check_hresult(stage->BindInput(0, inputTensor.get()));
    winrt::check_hresult(pipeline->Run());

    winrt::com_ptr<IWinMLTensor> outputTensor;
    winrt::check_hresult(stage->GetOutput(0, outputTensor.put()));

    winrt::com_ptr<IWinMLTensorDataLock> outputLock;
    winrt::check_hresult(outputTensor->Lock(
        WINML_TENSOR_LOCK_MODE_READ,
        WINML_TENSOR_LOCK_FLAG_NONE,
        outputLock.put()));

    BYTE* outputBytes = nullptr;
    UINT64 outputSizeInBytes = 0;
    winrt::check_hresult(outputLock->GetData(
        &outputBytes,
        &outputSizeInBytes));

    (void)outputBytes;
    std::wcout << L"Output bytes: " << outputSizeInBytes << L'\n';
}
```

Pass the execution target to both `AddModelStage` and the tensor factory to
keep model preparation and tensor placement on the same target.

`Lock` establishes a CPU-access window for the output tensor. It performs any
required mapping or synchronization, and the pointer returned by `GetData`
remains valid only while `outputLock` is alive.

## Call the helper

Call `RunModel` with the model path. For example, the following `wmain` accepts
the path on the command line:

```cpp
int wmain(int argc, wchar_t** argv)
{
    if (argc != 2)
    {
        std::wcerr << L"Usage: RuntimeApp.exe <model-path>\n";
        return 1;
    }

    RunModel(argv[1]);
    return 0;
}
```

Build and run the app:

```powershell
.\x64\Release\RuntimeApp.exe C:\models\model.onnx
```

Replace the path with your model. Interpret the returned tensor according to
that model's output schema; classification labels, token sampling, image
preprocessing, and other model-specific behavior belong in your app.

## Next steps

- [Windows ML Runtime API concepts](./concepts.md)
