---
author: eliotcowley
title: ITensorNative.GetBuffer method
description: Gets the tensor’s buffer as an array of bytes.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, GetBuffer
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- ITensorNative.GetBuffer
api_location:
- windows.ai.machinelearning.native.h
---

# ITensorNative.GetBuffer method

Gets the tensor’s buffer as an array of bytes.

```cpp
HRESULT GetBuffer(
    [out, size_is(, *capacity)] BYTE **value, 
    [out] UINT32 *capacity);
```

## Parameters

| Name | Type | Description |
|------|------|-------------|
| value | **BYTE**\*\* | The tensor's buffer. |
| capacity | **UINT32**\* | The capacity of the buffer. |

## Returns

**HRESULT**
The result of the operation.

## Examples

```cpp
TensorFloat SoftwareBitmapToSoftwareTensor(SoftwareBitmap softwareBitmap)
{
    // 1. Get access to the buffer of softwareBitmap
    BYTE* pData = nullptr;
    UINT32 size = 0;
    BitmapBuffer spBitmapBuffer(softwareBitmap.LockBuffer(BitmapBufferAccessMode::Read));
    winrt::Windows::Foundation::IMemoryBufferReference reference = spBitmapBuffer.CreateReference();
    auto spByteAccess = reference.as<::Windows::Foundation::IMemoryBufferByteAccess>();
    CHECK_HRESULT(spByteAccess->GetBuffer(&pData, &size));

    // ...
}
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
