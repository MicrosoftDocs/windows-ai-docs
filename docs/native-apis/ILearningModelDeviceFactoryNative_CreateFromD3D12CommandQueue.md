---
author: eliotcowley
title: ILearningModelDeviceFactoryNative.CreateFromD3D12CommandQueue method
description: Creates a LearningModelDevice that will run inference on the user-specified ID3D12CommandQueue.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, CreateFromD3D12CommandQueue
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- ILearningModelDeviceFactoryNative.CreateFromD3D12CommandQueue
api_location:
- windows.ai.machinelearning.native.h
---

# ILearningModelDeviceFactoryNative.CreateFromD3D12CommandQueue method

Creates a [LearningModelDevice](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevice) that will run inference on the user-specified [ID3D12CommandQueue](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12commandqueue).

```cpp
HRESULT CreateFromD3D12CommandQueue(
    ID3D12CommandQueue * value, 
    [out] IUnknown ** result);
```

## Parameters

| Name | Type | Description |
|------|------|-------------|
| value | [ID3D12CommandQueue](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12commandqueue)* | The **ID3D12CommandQueue** which the **LearningModelDevice** will be run against. |
| result | [IUnknown](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown)** | The **LearningModelDevice** to be created. |

## Returns

**HRESULT**
The result of the operation.

## Examples

```cpp
 // 1. create the d3d device.
com_ptr<ID3D12Device> pD3D12Device = nullptr;
CHECK_HRESULT(D3D12CreateDevice(
    nullptr, 
    D3D_FEATURE_LEVEL::D3D_FEATURE_LEVEL_11_0, 
    __uuidof(ID3D12Device), 
    reinterpret_cast<void**>(&pD3D12Device)));

// 2. create the command queue.
com_ptr<ID3D12CommandQueue> dxQueue = nullptr;
D3D12_COMMAND_QUEUE_DESC commandQueueDesc = {};
commandQueueDesc.Type = D3D12_COMMAND_LIST_TYPE_DIRECT;
CHECK_HRESULT(pD3D12Device->CreateCommandQueue(
    &commandQueueDesc, 
    __uuidof(ID3D12CommandQueue), 
    reinterpret_cast<void**>(&dxQueue)));
com_ptr<ILearningModelDeviceFactoryNative> devicefactory = 
    get_activation_factory<LearningModelDevice, ILearningModelDeviceFactoryNative>();
com_ptr<::IUnknown> spUnk;
CHECK_HRESULT(devicefactory->CreateFromD3D12CommandQueue(dxQueue.get(), spUnk.put()));
```

## See also

* [Custom Tensorization Sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization)

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../includes/get-help.md)]
