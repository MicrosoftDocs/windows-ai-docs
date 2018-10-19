---
author: eliotcowley
title: ITensorStaticsNative.CreateFromD3D12Resource method
description: Creates a tensor object (TensorFloat, TensorInt32Bit) from a user-specified ID3D12Resource.
ms.author: elcowle
ms.date: 10/05/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, CreateFromD3D12Resource
ms.localizationpriority: medium
---

# ITensorStaticsNative.CreateFromD3D12Resource method

Creates a tensor object ([TensorFloat](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat), [TensorInt32Bit](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorint32bit)) from a user-specified [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource).

```cpp
HRESULT CreateFromD3D12Resource(
    ID3D12Resource *value, 
    [size_is(shapeCount)] __int64 *shape, 
    int shapeCount, 
    [out] IUnknown ** result);
```

## Parameters

| Name | Type | Description |
|------|------|-------------|
| value | [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource)* | The **ID3D12Resource** from which to create the tensor. |
| shape | **__int64**\* | The shape of the tensor. |
| shapeCount | **int** | The number of dimensions of the tensor. |
| result | [IUnknown](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown)** | The resulting tensor. |

## Returns

**HRESULT**
The result of the operation.

## Examples

```cpp
TensorFloat SoftwareBitmapToDX12Tensor(SoftwareBitmap softwareBitmap)
{
    // ...
    
    // GPU tensorize
    com_ptr<ITensorStaticsNative> tensorfactory = get_activation_factory<TensorFloat, ITensorStaticsNative>();
    com_ptr<::IUnknown> spUnkTensor;
    TensorFloat input1imagetensor(nullptr);
    int64_t shapes[4] = { 1,3, softwareBitmap.PixelWidth(), softwareBitmap.PixelHeight() };
    CHECK_HRESULT(tensorfactory->CreateFromD3D12Resource(pGPUResource.get(), shapes, 4, spUnkTensor.put()));
    spUnkTensor.try_as(input1imagetensor);

    // ...
}
```

## See also

* [Custom Tensorization Sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization)

[!INCLUDE [help](../includes/get-help.md)]