---
author: eliotcowley
title: ITensorNative.GetBuffer method
description: Gets the tensor’s buffer as an array of bytes.
ms.author: elcowle
ms.date: 10/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, GetBuffer
ms.localizationpriority: medium
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

[!INCLUDE [help](../includes/get-help.md)]