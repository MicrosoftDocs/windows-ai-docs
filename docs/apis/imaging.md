---
title: Get Started with AI imaging in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) imaging features that will ship with the Windows App SDK and can be used to both scale and sharpen images as well as identify objects within an image.
ms.topic: article
ms.date: 11/08/2024
ms.author: kbridge
author: karl-bridge-microsoft
dev_langs:
- csharp
- cpp
---

# Get Started with AI imaging in the Windows App SDK

Imaging features will be supported by the [Windows App SDK](/windows/apps/windows-app-sdk/) through a set of artificial intelligence (AI)-backed APIs that can both scale and sharpen images (Image Super Resolution) as well as identify objects within an image (Image Segmentation).

For API details, see [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md).

> [!IMPORTANT]
> The Windows App SDK [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. They are not supported for use in production environments, and apps that use experimental features cannot be published to the Microsoft Store.

## Prerequisites

- [CoPilot+ PCs](/windows/ai/npu-devices/)
  > [!NOTE]
  > AMD-based CoPilot+ PCs do not support Image Super Resolution.

## What can I do with the Windows App SDK and Image Super Resolution?

Use the new AI Image Super Resolution features in the Windows App SDK to sharpen images and sharpen images while scaling them.

> [!NOTE]
> Scaling is limted to a maximum factor of 8x (higher scale factors can introduce artifacts and compromise image accuracy). If either the final width or height is greater than 8x their original values, an exception will be thrown.

### Increase sharpness of an image

This example shows how to change the scale (`targetWidth`, `targetHeight`) of an existing software bitmap image (`softwareBitmap`) and improve its sharpness (if you want to improve sharpness without scaling the image, specify the original image width and height).

1. First, we ensure the Image Super Resolution model is available by calling the IsAvailable method and waiting for the MakeAvailableAsync method to return successfully.
1. Once the Image Super Resolution model is available, we create an object to reference it.
1. We then get the final image by submitting the original image and the targeted width and height of the final image to the model using the ScaleSoftwareBitmap method.

```csharp
using Microsft.Windows.Imaging;
using Windows.Graphics.Imaging;

if (!ImageScaler.IsAvailable())
{
    var result = ImageScaler.MakeAvailableAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}

var imageScaler = await ImageScaler.CreateAsync();
var finalImage = imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight);
```

```cpp
#include "winrt/Microsoft.Graphics.Imaging.h" 
#include "winrt/Windows.Graphics.Imaging.h" 
using namespace winrt::Windows::Graphics::Imaging; 
using namespace winrt::Microsoft::Graphics::Imaging; 

if (!ImageScaler::IsAvailable()) 
{ 
    auto result = ImageScaler::MakeAvailableAsync(); 
    if (result.Status() != AsyncStatus::Completed) 
    { 
        throw result.ErrorCode(); 
    } 
}

ImageScaler imageScaler = ImageScaler::CreateAsync().get(); 
ImageBuffer buffer = imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight); 

```

## What can I do with the Windows App SDK and Image Segmentation?

Image Segmentation can be used to identify specific objects in an image. The model takes both an image and a "hints" object and returns a mask of the identified object. 

Hints can be provided through any combination of the following:

- Coordinates for points that belong to what you're identifying.
- Coordinates for points that don't belong to what you're identifying.
- A coordinate rectangle that encloses what you're identifying.

The more hints you provide, the more precise the model can be. However, follow these hint guidelines to avoid inaccurate results or errors.

- Avoid using multiple rectangles in a hint as they can produce an inaccurate mask.
- Avoid using exclude points exclusively without include points or a rectangle.
- Don't specify more than the supported maximum of 32 coordinates (1 for a point, 2 for a rectangle) as this will return an error.

The returned mask is in greyscale-8 format. The pixels of the identified object within the mask are 255 (the rest are 0 with no pixels holding any values in between).

### Identify an object within an image

The following examples show how the various ways you can identify an object within an image. We assume that you already have a software bitmap object you are trying to use as the input called softwareBitmap.

1. First, we ensure the Image Segmentation model is available by calling the IsAvailable method and waiting for the MakeAvailableAsync method to return successfully.
1. Once the Image Segmentation model is available, we create an object to reference it.
1. Next, we create an ImageObjectExtractor class by passing the image to ImageObjectExtractor.CreateWithImageBufferAsync (or CreateWithSoftwareBitmapAsync depending on your image type).
1. Then we'll create an ImageObjectExtractorHint object (we show other ways to create a hint object with different inputs later in this topic).
1. Finally, we submit the hint to the model using the GetSoftwareBitmapObjectMask method, which returns the final result.


```csharp
using Microsft.Windows.Imaging;
using Windows.Graphics.Imaging;

if (!ImageObjectExtractor.IsAvailable())
{
    var result = await ImageObjectExtractor.MakeAvailableAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}

ImageObjectExtractor imageObjectExtractor = 
  await ImageObjectExtractor.CreateWithSoftwareBitmapAsync(softwareBitmap);

ImageObjectExtractorHint hint(
    includeRects: null, 
    includePoints: 
        new List<PointInt32> { new PointInt32(306, 212), 
                               new PointInt32(216, 336)},
    excludePoints: null);
SoftwareBitmap finalImage = imageObjectExtractor.GetSoftwareBitmapObjectMask(hint);
```

```cpp
#include "winrt/Microsoft.Graphics.Imaging.h" 
#include "winrt/Windows.Graphics.Imaging.h" 
using namespace winrt::Windows::Graphics::Imaging; 
using namespace winrt::Microsoft::Graphics::Imaging; 

if (!ImageObjectExtractor::IsAvailable()) 
{ 
    auto result = ImageObjectExtractor::MakeAvailableAsync(); 
    if (result.Status() != AsyncStatus::Completed) 
    { 
        throw result.ErrorCode(); 
    } 
}

ImageObjectExtractor imageObjectExtractor = 
  ImageObjectExtractor::CreateWithSoftwareBitmapAsync(softwareBitmap).get();

ImageObjectExtractorHint hint(
    {}, 
    { 
        PointInt32{306, 212}, 
        PointInt32{216, 336} 
    },
    {}
);

SoftwareBitmap finalImage = imageObjectExtractor.GetSoftwareBitmapObjectMask(hint);
```

#### Specify hints with included and excluded points

In this code snippet, we demonstrate how to use both included and excluded points as hints.

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

```cpp
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

#### Specify hints with rectangle

In this code snippet, we demonstrate how to use a rectangle (RectInt32 is `X, Y, Width, Height`) as a hint.

```csharp
ImageObjectExtractorHint hint(
    includeRects: 
        new List<RectInt32> {new RectInt32(370, 278, 285, 126)},
    includePoints: null,
    excludePoints: null ); 
```

```cpp
ImageObjectExtractorHint hint(
    { 
        RectInt32{370, 278, 285, 126}
    }, 
    {},
    {}
);
```

## Additional resources

## Related content

- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
