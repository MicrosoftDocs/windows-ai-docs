---
title: Image Foreground Extractor with Windows AI APIs
description: Learn how to use the Image Foreground Extractor API to segment the foreground of an image using the Windows AI APIs.
ms.topic: how-to
ms.date: 11/17/2025
dev_langs:
- csharp
- cpp
---

# Image Foreground Extractor

Use [ImageForegroundExtractor](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageforegroundextractor) to segment the foreground of an input image and enable features such as background removal and sticker generation.

The returned mask is in greyscale-8 format. Pixel values range from 0 to 255, where 0 represents background pixels, 255 represents foreground pixels, and intermediate values indicate a blend of foreground and background pixels.

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

For **content moderation details**, see [Content safety with generative AI APIs](content-moderation.md).

[!INCLUDE [AI APIs package manifest requirement](./includes/ai-apis-package-manifest-requirements.md)]

## Generating a mask from a bitmap image

1. Call [GetReadyState](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageforegroundextractor.getreadystate) and wait for [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageforegroundextractor.ensurereadyasync) to complete successfully to confirm that the ImageForegroundExtractor object is ready.
2. After the model is ready, call [CreateAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageforegroundextractor.createasync) to instantiate an ImageForegroundExtractor object.
3. Call [GetMaskFromSoftwareBitmap](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageforegroundextractor.getmaskfromsoftwarebitmap) with the input image to generate the foreground mask.

```csharp
using Microsoft.Windows.AI.Imaging;
using Microsoft.Windows.AI;

if (ImageForegroundExtractor.GetReadyState() == AIFeatureReadyState.NotReady)
{
    var result  = await ImageForegroundExtractor.EnsureReadyAsync();
    if (result.Status != AIFeatureReadyResultState.Success)
    {
        throw result.ExtendedError;
    }
}

var model = await ImageForegroundExtractor.CreateAsync();

// Insert your own softwareBitmap here.
var foregroundMask = model.GetMaskFromSoftwareBitmap(softwareBitmap);
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

if (ImageForegroundExtractor::GetReadyState() == AIFeatureReadyState::NotReady)
{
    auto loadResult = ImageForegroundExtractor::EnsureReadyAsync().get();

    if (loadResult.Status() != AIFeatureReadyResultState::Success)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
}

auto model = co_await ImageForegroundExtractor::CreateAsync();

// Insert your own softwareBitmap here.
auto foregroundMask = model.GetMaskFromSoftwareBitmap(softwareBitmap);

```

## See also

- [AI Imaging overview](imaging.md)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
