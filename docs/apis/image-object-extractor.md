---
title: Image Object Extractor with Windows AI APIs
description: Learn how to use the Image Object Extractor API to identify specific objects in an image using the Windows AI APIs.
ms.topic: how-to
ms.date: 11/17/2025
dev_langs:
- csharp
- cpp
---

# Image Object Extractor

Image Object Extractor can be used to identify specific objects in an image. The model takes both an image and a "hints" object and returns a mask of the identified object.

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

For **content moderation details**, see [Content safety with generative AI APIs](content-moderation.md).

[!INCLUDE [AI APIs package manifest requirement](./includes/ai-apis-package-manifest-requirements.md)]

## Providing hints

Hints can be provided through any combination of the following:

- Coordinates for points that belong to what you're identifying.
- Coordinates for points that don't belong to what you're identifying.
- A coordinate rectangle that encloses what you're identifying.

The more hints you provide, the more precise the model can be. Follow these hint guidelines to minimize inaccurate results or errors.

- Avoid using multiple rectangles in a hint as they can produce an inaccurate mask.
- Avoid using exclude points exclusively without include points or a rectangle.
- Don't specify more than the supported maximum of 32 coordinates (1 for a point, 2 for a rectangle) as this will return an error.

The returned mask is in greyscale-8 format with the pixels of the mask for the identified object having a value of 255 (all others having a value of 0).

## Image Object Extractor example

The following examples show ways to identify an object within an image. The examples assume that you already have a software bitmap object (`softwareBitmap`) for the input.

1. Ensure the Image Object Extractor model is available by calling the [GetReadyState](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectextractor.getreadystate) method and waiting for the [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectextractor.ensurereadyasync) method to return successfully.

2. Once the Image Object Extractor model is available, create an [ImageObjectExtractor](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectextractor) object to reference it.

3. Pass the image to [CreateWithSoftwareBitmapAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectextractor.createwithsoftwarebitmapasync).

4. Create an [ImageObjectExtractorHint](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectextractorhint) object. Other ways to create a hint object with different inputs are demonstrated later.

5. Submit the hint to the model using the [GetSoftwareBitmapObjectMask](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imageobjectextractor.getsoftwarebitmapobjectmask) method, which returns the final result.

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.AI;
using Microsoft.Windows.Management.Deployment;
using Windows.Graphics.Imaging;

if (ImageObjectExtractor.GetReadyState() == AIFeatureReadyState.NotReady) 
{
    var result = await ImageObjectExtractor.EnsureReadyAsync();
    if (result.Status != AIFeatureReadyResultState.Success)
    {
        throw result.ExtendedError;
    }
}

ImageObjectExtractor imageObjectExtractor = await ImageObjectExtractor.CreateWithSoftwareBitmapAsync(softwareBitmap);

ImageObjectExtractorHint hint = new ImageObjectExtractorHint{
    includeRects: null, 
    includePoints:
        new List<PointInt32> { new PointInt32(306, 212),
                               new PointInt32(216, 336)},
    excludePoints: null};
SoftwareBitmap finalImage = imageObjectExtractor.GetSoftwareBitmapObjectMask(hint);
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

if (ImageObjectExtractor::GetReadyState() == AIFeatureReadyState::NotReady)
{
    auto loadResult = ImageObjectExtractor::EnsureReadyAsync().get();

    if (loadResult.Status() != AIFeatureReadyResultState::Success)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
}

ImageObjectExtractor imageObjectExtractor = ImageObjectExtractor::CreateWithSoftwareBitmapAsync(softwareBitmap).get();

ImageObjectExtractorHint hint(
    {},
    {
        Windows::Graphics::PointInt32{306, 212},        
        Windows::Graphics::PointInt32{216, 336}
    },
    {}
);

Windows::Graphics::Imaging::SoftwareBitmap finalImage = imageObjectExtractor.GetSoftwareBitmapObjectMask(hint);
```

### Specify hints with included and excluded points

This code snippet demonstrates how to use both included and excluded points as hints.

```csharp
ImageObjectExtractorHint hint(
    includeRects: null,
    includePoints: 
        new List<PointInt32> { new PointInt32(150, 90), 
                               new PointInt32(216, 336), 
                               new PointInt32(550, 330)},
    excludePoints: 
        new List<PointInt32> { new PointInt32(306, 212) });
```

```cppwinrt
ImageObjectExtractorHint hint(
    {}, 
    { 
        PointInt32{150, 90}, 
        PointInt32{216, 336}, 
        PointInt32{550, 330}
    },
    { 
        PointInt32{306, 212}
    }
);
```

### Specify hints with rectangle

This code snippet demonstrates how to use a rectangle (RectInt32 is `X, Y, Width, Height`) as a hint.

```csharp
ImageObjectExtractorHint hint(
    includeRects: 
        new List<RectInt32> {new RectInt32(370, 278, 285, 126)},
    includePoints: null,
    excludePoints: null ); 
```

```cppwinrt
ImageObjectExtractorHint hint(
    { 
        RectInt32{370, 278, 285, 126}
    }, 
    {},
    {}
);
```

## See also

- [AI Imaging overview](imaging.md)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
