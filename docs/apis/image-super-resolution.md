---
title: Image Super Resolution with Windows AI APIs
description: Learn how to use the Image Super Resolution API to scale and sharpen images using the Windows AI APIs.
ms.topic: how-to
ms.date: 11/17/2025
dev_langs:
- csharp
- cpp
---

# Image Super Resolution

The Image Super Resolution APIs enable image sharpening and scaling.

Scaling is limited to a maximum factor of 8x as higher scale factors can introduce artifacts and compromise image accuracy. If either the final width or height is greater than 8x their original values, an exception will be thrown.

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

For **content moderation details**, see [Content safety with generative AI APIs](content-moderation.md).

[!INCLUDE [AI APIs package manifest requirement](./includes/ai-apis-package-manifest-requirements.md)]

## Image Super Resolution example

The following example shows how to change the scale (`targetWidth`, `targetHeight`) of an existing software bitmap image (`softwareBitmap`) and improve the image sharpness using an `ImageScaler` object  (to improve sharpness without scaling the image, simply specify the existing image width and height).

1. Ensure the Image Super Resolution model is available by calling the [GetReadyState](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagescaler.getreadystate) method and then waiting for the [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagescaler.ensurereadyasync) method to return successfully.

2. Once the Image Super Resolution model is available, create an [ImageScaler](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagescaler) object to reference it.

3. Get a sharpened and scaled version of the existing image by passing the existing image and the desired width and height to the model using the [ScaleSoftwareBitmap](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagescaler.scalesoftwarebitmap) method.

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.Management.Deployment;
using Microsoft.Windows.AI;
using Windows.Graphics.Imaging;

if (ImageScaler.GetReadyState() == AIFeatureReadyState.NotReady) 
{
    var result = await ImageScaler.EnsureReadyAsync();
    if (result.Status != AIFeatureReadyResultState.Success)
    {
        throw result.ExtendedError;
    }
}
ImageScaler imageScaler = await ImageScaler.CreateAsync();
SoftwareBitmap finalImage = imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight);
```

```cppwinrt
#include <winrt/Microsoft.Graphics.Imaging.h>
#include <winrt/Microsoft.Windows.AI.h>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Graphics.Imaging.h>

using namespace winrt::Microsoft::Graphics::Imaging;
using namespace winrt::Microsoft::Windows::AI;
using namespace winrt::Windows::Foundation; 
using namespace winrt::Windows::Graphics::Imaging; 

if (ImageScaler::GetReadyState() == AIFeatureReadyState::NotReady)
{
    auto loadResult = ImageScaler::EnsureReadyAsync().get();

    if (loadResult.Status() != AIFeatureReadyResultState::Success)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
}
int targetWidth = 100;
int targetHeight = 100;
ImageScaler imageScaler = ImageScaler::CreateAsync().get();
Windows::Graphics::Imaging::SoftwareBitmap finalImage = 
    imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight);
```

## See also

- [AI Imaging overview](imaging.md)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
