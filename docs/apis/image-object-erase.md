---
title: Image Object Erase with Windows AI APIs
description: Learn how to use the Image Object Erase API to remove objects from images using the Windows AI APIs.
ms.topic: how-to
ms.date: 11/17/2025
dev_langs:
- csharp
- cpp
---

# Image Object Erase

Object Erase can be used to remove objects from images. The model takes both an image and a greyscale mask indicating the object to be removed, erases the masked area from the image, and replaces the erased area with the image background.

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

For **content moderation details**, see [Content safety with generative AI APIs](content-moderation.md).

[!INCLUDE [AI APIs package manifest requirement](./includes/ai-apis-package-manifest-requirements.md)]

## Image Object Erase example

The following example shows how to remove an object from an image. The example assumes that you already have software bitmap objects (`softwareBitmap`) for the both the image and the mask. The mask must be in Gray8 format with each pixel of the area to be removed set to 255 and all other pixels set to 0.

1. Ensure the Image Object Erase model is available by calling the [GetReadyState](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectremover.getreadystate) method and waiting for the [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectremover.ensurereadyasync) method to return successfully.
1. Once the Image Object Erase model is available, create an [ImageObjectRemover](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectremover) object to reference it.
1. Finally, submit the image and the mask to the model using the [RemoveFromSoftwareBitmap](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectremover.removefromsoftwarebitmap) method, which returns the final result.

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.AI;
using Microsoft.Windows.Management.Deployment;
using Windows.Graphics.Imaging;

if (ImageObjectRemover.GetReadyState() == AIFeatureReadyState.NotReady) 
{
    var result = await ImageObjectRemover.EnsureReadyAsync();
    if (result.Status != AIFeatureReadyResultState.Success)
    {
        throw result.ExtendedError;
    }
}
ImageObjectRemover imageObjectRemover = await ImageObjectRemover.CreateAsync();
SoftwareBitmap finalImage = imageObjectRemover.RemoveFromSoftwareBitmap(imageBitmap, maskBitmap); // Insert your own imagebitmap and maskbitmap
```

```cppwinrt
#include <winrt/Microsoft.Graphics.Imaging.h>
#include <winrt/Microsoft.Windows.AI.Imaging.h>
#include <winrt/Windows.Graphics.Imaging.h>
#include <winrt/Windows.Foundation.h>
using namespace winrt::Microsoft::Graphics::Imaging;
using namespace winrt::Microsoft::Windows::AI.Imaging;
using namespace winrt::Windows::Graphics::Imaging; 
using namespace winrt::Windows::Foundation;
if (ImageObjectRemover::GetReadyState() == AIFeatureReadyState::NotReady)
{
    auto loadResult = ImageObjectRemover::EnsureReadyAsync().get();

    if (loadResult.Status() != AIFeatureReadyResultState::Success)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
}

ImageObjectRemover imageObjectRemover = ImageObjectRemover::CreateAsync().get();
// Insert your own imagebitmap and maskbitmap
Windows::Graphics::Imaging::SoftwareBitmap buffer = 
    imageObjectRemover.RemoveFromSoftwareBitmap(imageBitmap, maskBitmap);
```

## See also

- [AI Imaging overview](imaging.md)
- [Image Object Extractor](image-object-extractor.md) - Use this to generate masks for objects you want to remove
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
