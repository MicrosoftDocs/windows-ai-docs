---
title: Get Started with AI imaging in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) imaging features that will ship with the Windows App SDK and can be used to both scale and sharpen images as well as identify objects within an image.
ms.topic: get-started
ms.date: 09/17/2025
dev_langs:
- csharp
- cpp
---

# Get Started with AI Imaging

Imaging features in Windows AI Foundry support the following capabilities:

- [**Image Super Resolution**](#what-can-i-do-with-image-super-resolution): scaling and sharpening an image.
- [**Image Description**](#what-can-i-do-with-image-description): generating text that describes an image.
- [**Image Segmentation**](#what-can-i-do-with-image-segmentation): identifying objects within an image.
- [**Object Erase**](#what-can-i-do-with-object-erase): removing objects from an image.

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

For **content moderation details**, see  [Content safety with generative AI APIs](content-moderation.md).

## What can I do with Image Super Resolution?

The Image Super Resolution APIs enable image sharpening and scaling.

Scaling is limited to a maximum factor of 8x as higher scale factors can introduce artifacts and compromise image accuracy. If either the final width or height is greater than 8x their original values, an exception will be thrown.

## More details on Image Scaler

The following example shows how to change the scale (`targetWidth`, `targetHeight`) of an existing software bitmap image (`softwareBitmap`) and improve the image sharpness (to improve sharpness without scaling the image, simply specify the existing image width and height) using an `ImageScaler` object.

1. Ensure the Image Super Resolution model is available by calling the **ImageScaler.GetReadyState** method and then waiting for the **ImageScaler.EnsureReadyAsync** method to return successfully.

2. Once the Image Super Resolution model is available, create an **ImageScaler** object to reference it.

3. Get a sharpened and scaled version of the existing image by passing the existing image and the desired width and height to the model using the **ScaleSoftwareBitmap** method.

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.Management.Deployment;
using Microsoft.Windows.AI;
using Windows.Graphics.Imaging;

if (ImageScaler.GetReadyState() == AIFeatureReadyState.EnsureNeeded) 
{
    var result = await ImageScaler.EnsureReadyAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}
ImageScaler imageScaler = await ImageScaler.CreateAsync();
SoftwareBitmap finalImage = imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight);
```

```cpp
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

## What can I do with Image Description?

> [!IMPORTANT]
> Image Description is currently unavailable in China.

The Image Description APIs provide the ability to generate various types of text descriptions for an image.

The following types of text descriptions are supported:

- [**Brief**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a description suitable for charts and diagrams.
- [**Detailed**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a long description.
- [**Diagram**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a short description suitable for an image caption. The default if no value is specified.
- [**Accessible**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a long description with details intended for users with accessibility needs.

Because these APIs use Machine Learning (ML) models, occasional errors can occur where the text does not describe the image correctly. Therefore, we do not recommend using these APIs for images in the following scenarios:

- Where the images contain potentially sensitive content and inaccurate descriptions could be controversial, such as flags, maps, globes, cultural symbols, or religious symbols.
- When accurate descriptions are critical, such as for medical advice or diagnosis, legal content, or financial documents.

### Get text description from an image

The Image Description API takes an image, the desired text description type (optional), and the level of content moderation you want to employ (optional) to protect against harmful use.

The following example shows how to get a text description for an image.

> [!NOTE]
> The image must be an **ImageBuffer** object as **SoftwareBitmap** is not currently supported. This example demonstrates how to convert **SoftwareBitmap** to **ImageBuffer**.

1. Ensure the Image Super Resolution model is available by calling the **ImageDescriptionGenerator.GetReadyState** method and then waiting for the **ImageDescriptionGenerator.EnsureReadyAsync** method to return successfully.

2. Once the Image Super Resolution model is available, create an **ImageDescriptionGenerator** object to reference it.

3. (Optional) Create a **ContentFilterOptions** object and specify your preferred values. If you choose to use default values, you can pass in a null object.

4. Get the image description (**LanguageModelResponse.Response**) by calling the [**ImageDescriptionGenerator.DescribeAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptiongenerator.describeasync) method specifying the original image, the [description kind](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) (an optional value for the preferred description type), and the [**ContentFilterOptions**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.contentfilteroptions) object (optional).

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.Management.Deployment;  
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.ContentModeration;
using Windows.Storage.StorageFile;  
using Windows.Storage.Streams;  
using Windows.Graphics.Imaging;

if (ImageDescriptionGenerator.GetReadyState() == AIFeatureReadyState.EnsureNeeded) 
{
    var result = await ImageDescriptionGenerator.EnsureReadyAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}

ImageDescriptionGenerator imageDescriptionGenerator = await ImageDescriptionGenerator.CreateAsync();

// Convert already available softwareBitmap to ImageBuffer.
ImageBuffer inputImage = ImageBuffer.CreateCopyFromBitmap(softwareBitmap);  

// Create content moderation thresholds object.
ContentFilterOptions filterOptions = new ContentFilterOptions();
filterOptions.PromptMinSeverityLevelToBlock.ViolentContentSeverity = SeverityLevel.Medium;
filterOptions.ResponseMinSeverityLevelToBlock.ViolentContentSeverity = SeverityLevel.Medium;

// Get text description.
LanguageModelResponse languageModelResponse = await imageDescriptionGenerator.DescribeAsync(inputImage, ImageDescriptionScenario.Caption, filterOptions);
string response = languageModelResponse.Response;

```

```cpp
#include <winrt/Microsoft.Graphics.Imaging.h>
#include <winrt/Microsoft.Windows.AI.Imaging.h>
#include <winrt/Microsoft.Windows.AI.ContentSafety.h>
#include <winrt/Microsoft.Windows.AI.h>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Graphics.Imaging.h> 
#include <winrt/Windows.Storage.Streams.h>
#include <winrt/Windows.Storage.StorageFile.h>

using namespace winrt::Microsoft::Graphics::Imaging; 
using namespace winrt::Microsoft::Windows::AI;
using namespace winrt::Microsoft::Windows::AI::ContentSafety; 
using namespace winrt::Microsoft::Windows::AI::Imaging; 
using namespace winrt::Windows::Foundation; 
using namespace winrt::Windows::Graphics::Imaging;
using namespace winrt::Windows::Storage::Streams;
using namespace winrt::Windows::Storage::StorageFile;    

if (ImageDescriptionGenerator::GetReadyState() == AIFeatureReadyState::NotReady)
{
    auto loadResult = ImageDescriptionGenerator::EnsureReadyAsync().get();
    auto loadResult = ImageScaler::EnsureReadyAsync().get();

    if (loadResult.Status() != AIFeatureReadyResultState::Success)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
}

ImageDescriptionGenerator imageDescriptionGenerator = 
    ImageDescriptionGenerator::CreateAsync().get();

// Convert already available softwareBitmap to ImageBuffer.
auto inputBuffer = Microsoft::Graphics::Imaging::ImageBuffer::CreateForSoftwareBitmap(bitmap); (softwareBitmap);

// Create content moderation thresholds object.

ContentFilterOptions contentFilter{};
contentFilter.PromptMaxAllowedSeverityLevel().Violent(SeverityLevel::Medium);
contentFilter.ResponseMaxAllowedSeverityLevel().Violent(SeverityLevel::Medium);

// Get text description.
auto response = imageDescriptionGenerator.DescribeAsync(inputImage, ImageDescriptionKind::BriefDescription, contentFilter).get();
string text = response.Description();
```

## What can I do with Image Segmentation?

Image Segmentation can be used to identify specific objects in an image. The model takes both an image and a "hints" object and returns a mask of the identified object.

Hints can be provided through any combination of the following:

- Coordinates for points that belong to what you're identifying.
- Coordinates for points that don't belong to what you're identifying.
- A coordinate rectangle that encloses what you're identifying.

The more hints you provide, the more precise the model can be. Follow these hint guidelines to minimize inaccurate results or errors.

- Avoid using multiple rectangles in a hint as they can produce an inaccurate mask.
- Avoid using exclude points exclusively without include points or a rectangle.
- Don't specify more than the supported maximum of 32 coordinates (1 for a point, 2 for a rectangle) as this will return an error.

The returned mask is in greyscale-8 format with the pixels of the the mask for the identified object having a value of 255 (all others having a value of 0).

### Identify an object within an image

The following examples show ways to identify an object within an image. The examples assume that you already have a software bitmap object (`softwareBitmap`) for the input.

1. Ensure the Image Segmentation model is available by calling the **GetReadyState** method and waiting for the **EnsureReadyAsync** method to return successfully.

2. Once the Image Segmentation model is available, create an **ImageObjectExtractor** object to reference it.

3. Pass the image to **ImageObjectExtractor.CreateWithSoftwareBitmapAsync**.

4. Create an **ImageObjectExtractorHint** object. Other ways to create a hint object with different inputs are demonstrated later.

5. Submit the hint to the model using the **GetSoftwareBitmapObjectMask** method, which returns the final result.

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.AI;
using Microsoft.Windows.Management.Deployment;
using Windows.Graphics.Imaging;

if (ImageObjectExtractor::GetReadyState() == AIFeatureReadyState.EnsureNeeded) 
{
    var result = await ImageObjectExtractor.EnsureReadyAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
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

```cpp
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

#### Specify hints with included and excluded points

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

This code snippet demonstrates how to use a rectangle (RectInt32 is `X, Y, Width, Height`) as a hint.

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

## What can I do with Object Erase?

Object Erase can be used to remove objects from images. The model takes both an image and a greyscale mask indicating the object to be removed, erases the masked area from the image, and replaces the erased area with the image background.

### Remove Unwanted Objects From an Image

The following example shows how to remove an object from an image. The example assumes that you already have software bitmap objects (`softwareBitmap`) for the both the image and the mask. The mask must be in Gray8 format with each pixel of the area to be removed set to 255 and all other pixels set to 0.

1. Ensure the Image Segmentation model is available by calling the **GetReadyState** method and waiting for the **EnsureReadyAsync** method to return successfully.
1. Once the Object Erase model is available, create an **ImageObjectRemover** object to reference it.
1. Finally, submit the image and the mask to the model using the **RemoveFromSoftwareBitmap** method, which returns the final result.

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.AI;
using Microsoft.Windows.Management.Deployment;
using Windows.Graphics.Imaging;

if (ImageObjectRemover::GetReadyState() == AIFeatureReadyState.EnsureNeeded) 
{
    var result = await ImageObjectRemover.EnsureReadyAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}
ImageObjectRemover imageObjectRemover = await ImageObjectRemover.CreateAsync();
SoftwareBitmap finalImage = imageObjectRemover.RemoveFromSoftwareBitmap(imageBitmap, maskBitmap); // Insert your own imagebitmap and maskbitmap
```

```cpp
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

## Responsible AI

We have used a combination of the following steps to ensure these imaging APIs are trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](../rai.md) when implementing AI features in your app.

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
