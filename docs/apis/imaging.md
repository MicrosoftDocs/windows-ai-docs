---
title: Get Started with AI imaging in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) imaging features that will ship with the Windows App SDK and can be used to both scale and sharpen images as well as identify objects within an image.
ms.topic: article
ms.date: 01/07/2024
ms.author: kbridge
author: karl-bridge-microsoft
dev_langs:
- csharp
- cpp
---

# Get Started with AI imaging in the Windows App SDK

> [!IMPORTANT]
> **This feature is available on the [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes. These APIs may be removed from subsequent releases at any time. They are not supported for use in production environments. Apps that use experimental features cannot be published to the Microsoft Store.

Imaging features will be supported by the [Windows App SDK](/windows/apps/windows-app-sdk/) through a set of AI-backed APIs that can perform actions including:

- **Image Super Resolution**: scaling and sharpening images
- **Image Description**: producing text that describes the image
- **Image Segmentation**: identifying objects within an image

For API details, see [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md). To provide feedback on this API, [create or upvote an issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo and include **Imaging** in the title.

## Prerequisites

- A [CoPilot+ PC](/windows/ai/npu-devices/) is required to use this feature. AMD-based CoPilot+ PCs do not currently support Image Super Resolution.

## What can I do with Image Super Resolution?

Use the new AI Image Super Resolution features in the Windows App SDK to sharpen and scale images.

Scaling is limited to a maximum factor of 8x. Higher scale factors can introduce artifacts and compromise image accuracy. If either the final width or height is greater than 8x their original values, an exception will be thrown.

### Increase sharpness of an image

This example shows how to change the scale (`targetWidth`, `targetHeight`) of an existing software bitmap image (`softwareBitmap`) and improve the image sharpness. To improve sharpness without scaling the image, specify the original image width and height.

1. Ensure the Image Super Resolution model is available by calling the `IsAvailable` method and waiting for the `MakeAvailableAsync` method to return successfully.

2. Once the Image Super Resolution model is available, create an object to reference it.

3. The final image is returned by submitting the original image and the targeted width and height of the final image to the model using the `ScaleSoftwareBitmap` method.

```csharp
using Microsft.Windows.Imaging;
using Microsoft.Windows.Management.Deployment;
using Windows.Graphics.Imaging;


if (!ImageScaler.IsAvailable())
{
    var result = co_await ImageScaler.MakeAvailableAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}

ImageScalar imageScaler = await ImageScaler.CreateAsync();
SoftwareBitmap finalImage = imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight);
```

```cpp
#include <winrt/Microsoft.Graphics.Imaging.h>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Graphics.Imaging.h>

using namespace winrt::Microsoft::Graphics::Imaging;
using namespace winrt::Windows::Foundation; 
using namespace winrt::Windows::Graphics::Imaging; 

 
if (!ImageScaler::IsAvailable()) 
{ 
    auto result = ImageScaler::MakeAvailableAsync(); 
    if (result.Status() != AsyncStatus::Completed) 
    { 
        throw result.ErrorCode(); 
    } 
}

ImageScaler imageScaler = ImageScaler::CreateAsync().get(); 
SoftwareBitmap finalImage = imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight);
```

## What can I do with Image Description?

Image Description can be used to get a text description for an image. The main use cases for this API are to get descriptions of varying length for images. Image descriptions may include a short caption or a long description for users with accessibility needs.

Given that these APIs use Machine Learning (ML) models, there will be occasional errors where the text does not approporiately match the image. For this reason, these APIs should not be used for images that feature sensitive content, such as flags, maps, globes, cultural symbols, or religious symbols, where inaccurate descriptions could create controversy. Additionally, these APIs should not be used for scenarios that require high confidence in the description accuracy, such as use in medical advice or diagnosis, legal content, or financial documents.

The Image Description API takes an image, an enum to specify what type of textual description you're looking for, and a `ContentFilterOptions` object that allows you to specify the level of content moderation you want to employ. The `ContentFilterOptions` and the enum for text description are both optional parameters.

Currently, this enum supports four different styles of textual description:

- **Caption** - Provides a plain description of image. The default if no value is specified.
- **Accessibility** - Provides a lengthy description that includes a focus on serving users with accessibility needs.
- **Detailed Description** - Provides a lengthy description of the image with specific details.
- **Office Charts** - Best suited for describing images of charts, diagrams, and other figures in detail.

```
enum ImageDescriptionScenario 
{ 
    Accessibility       = 0x01, 
    Caption             = 0x02, 
    DetailedNarration   = 0x03, 
    OfficeCharts        = 0x04, 
};
```

The Image Description API runs text content moderation to protect against harmful uses and provides the ability to alter the thresholds set for triggering.

The Image Description API is overloaded with multiple different parameter sets based on the level of control you want over the API. Shown below are the different methods. The example below illustrates a scenario where all parameters are used.

```
public IAsyncOperationWithProgress<LanguageModelResponse, String> DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer image);

public IAsyncOperationWithProgress<LanguageModelResponse, String> DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer image,
    ImageDescriptionScenario scenario);

public IAsyncOperationWithProgress<LanguageModelResponse, String> DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer image,
    ImageDescriptionScenario scenario,
    ContentFilterOptions contentFilterOptions);

```

### Get text description from an image

The following example shows how to get a text description for an image. The image must be an `ImageBuffer` object. `SoftwareBitmap` is not currently supported for this API. There is an API that allows you to convert `SoftwareBitmap` to `ImageBuffer` demonstrated in the code below.

1. Ensure the Image Description API models are available by calling the `IsAvailable` + `MakeAvailable` methods and waiting for the methods to return successfully.

2. Once the models are available, create an object to reference it.

3. (Optional) Create a `ContentFilterOptions` object and specify your preferred values. If you choose to use default values, you can pass in a null object.

4. Return the final image by submitting the original image, an enum for the preferred style of textual description (optional), and the `ContentFilterOptions` object. 

```csharp
using Microsft.Graphics.Imaging;
using Microsoft.Windows.Management.Deployment;  
using Microsoft.Windows.AI.Generative;
using Microsoft.Windows.AI.ContentModeration;
using Windows.Storage.StorageFile;  
using Windows.Storage.Streams;  
using Windows.Graphics.Imaging;

if (!ImageDescriptionGenerator.IsAvailable())
{
    var result = await ImageDescriptionGenerator.MakeAvailableAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}

ImageDescriptionGenerator imageDescriptionGenerator = await ImageDescriptionGenerator.CreateAsync();

// Grab image from file
StorageFile file = await GetFileFromPathAsync("<ImagePath>");  
IRandomAccessStream stream = await file.OpenReadAsync();  
BitmapDecoder decoder = await BitmapDecoder.CreateAsync(stream);  
SoftwareBitmap imageBitmap = await decoder.GetSoftwareBitmapAsync();  
ImageBuffer inputImage = ImageBuffer.CreateCopyFromBitmap(imageBitmap);  

// Create content moderation thresholds object
ContentFilterOptions filterOptions = new ContentFilterOptions();  
filterOptions.PromptMinSeverityLevelToBlock.ViolentContentSeverity = SeverityLevel.Medium;  
filterOptions.ResponseMinSeverityLevelToBlock.ViolentContentSeverity = SeverityLevel.Medium;  

// Get text description
LanguageModelResponse languageModelResponse = await imageDescriptionGenerator.DescribeAsync(inputImage, ImageDescriptionScenario.Caption, filterOptions);
string response = languageModelResponse.Response;

```

```cpp
#include <winrt/Microsoft.Graphics.Imaging.h>
#include <winrt/Microsoft.Windows.AI.ContentModeration.h>
#include <winrt/Microsoft.Windows.AI.Generative.h>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Graphics.Imaging.h> 
#include <winrt/Windows.Storage.Streams.h>
#include <winrt/Windows.Storage.StorageFile.h>
using namespace winrt::Microsoft::Graphics::Imaging; 
using namespace winrt::Microsoft::Windows::AI::ContentModeration; 
using namespace winrt::Microsoft::Windows::AI::Generative; 
using namespace winrt::Windows::Foundation; 
using namespace winrt::Windows::Graphics::Imaging;
using namespace winrt::Windows::Storage::Streams;
using namespace winrt::Windows::Storage::StorageFile;

if (!ImageDescriptionGenerator::IsAvailable()) 
{ 
    auto result = co_await ImageDescriptionGenerator::MakeAvailableAsync(); 
    if (result.Status() != AsyncStatus::Completed) 
    { 
        throw result.ErrorCode(); 
    } 
}

ImageDescriptionGenerator imageDescriptionGenerator = co_await ImageDescriptionGenerator::CreateAsync(); 
// Grab image from file
auto file = co_await GetFileFromPathAsync("<ImagePath>"); 
auto stream = co_await file.OpenReadAsync(); 
auto decoder = co_await BitmapDecoder::CreateAsync(stream); 
auto imageBitmap = co_await decoder.GetSoftwareBitmapAsync(); 
auto inputBuffer = ImageBuffer::CreateCopyFromBitmap(imageBitmap); 

// Create content moderation thresholds object
ContentFilterOptions contentFilter = ContentFilterOptions(); 
contentFilter.PromptMinSeverityLevelToBlock().ViolentContentSeverity(SeverityLevel::Medium); 
contentFilter.ResponseMinSeverityLevelToBlock().ViolentContentSeverity(SeverityLevel::Medium); 

// Get text description
LanguageModelResponse languageModelResponse = co_await imageDescriptionGenerator.DescribeAsync(inputImage, ImageDescriptionScenario.Caption, contentFilter);
string text = languageModelResponse.Response;
```

## What can I do with Image Segmentation?

Image Segmentation can be used to identify specific objects in an image. The model takes both an image and a "hints" object and returns a mask of the identified object. 

Hints can be provided through any combination of the following:

- Coordinates for points that belong to what you're identifying.
- Coordinates for points that don't belong to what you're identifying.
- A coordinate rectangle that encloses what you're identifying.

The more hints you provide, the more precise the model can be. Follow these hint guidelines to avoid inaccurate results or errors.

- Avoid using multiple rectangles in a hint as they can produce an inaccurate mask.
- Avoid using exclude points exclusively without include points or a rectangle.
- Don't specify more than the supported maximum of 32 coordinates (1 for a point, 2 for a rectangle) as this will return an error.

The returned mask is in greyscale-8 format. The pixels of the identified object within the mask are 255 (the rest are 0 with no pixels holding any values inbetween).

### Identify an object within an image

The following examples show ways to identify an object within an image. The examples assume that you already have a software bitmap object (`softwareBitmap`) for the input.

1. Ensure the Image Segmentation model is available by calling the `IsAvailable` method and waiting for the `MakeAvailableAsync` method to return successfully.

2. Once the Image Segmentation model is available, create an object to reference it.

3. Create an `ImageObjectExtractor` class by passing the image to `CreateWithSoftwareBitmapAsync`.

4. Create an `ImageObjectExtractorHint` object. Alternative ways to create a hint object with different inputs is demonstrated later in this topic.

5. Submit the hint to the model using the `GetSoftwareBitmapObjectMask` method, which returns the final result.


```csharp
using Microsft.Windows.Imaging;
using Microsoft.Windows.Management.Deployment;
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
#include <winrt/Microsoft.Graphics.Imaging.h> 
#include <winrt/Windows.Graphics.Imaging.h>
#include <winrt/Windows.Foundation.h>
using namespace winrt::Microsoft::Graphics::Imaging; 
using namespace winrt::Windows::Graphics::Imaging; 
using namespace winrt::Windows::Foundation; 


if (!ImageObjectExtractor::IsAvailable()) 
{ 
    auto result = co_await ImageObjectExtractor::MakeAvailableAsync(); 
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

## Responsible AI

These imaging APIs provide developers with powerful, trustworthy models for building apps with safe, secure AI experiences. We have used a combination of the following steps to ensure these imaging APIs are trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](/windows/ai/rai) when implementing AI features in your app.

- Thorough testing and evaluation of the model quality to identify and mitigate potential risks.
- Incremental roll out of imaging API experimental releases. Following the final experimental release, the roll out will expand to signed apps to ensure that malware scans have been applied to applications with local model capabilities.
- Provide a local AI model for content moderation that identifies and filters harmful content in both the input and AI-generated output of any APIs that use generative AI models. This local content moderation model is based on the [Azure AI Content Safety](https://azure.microsoft.com/products/ai-services/ai-content-safety) model for text moderation and provides similar performance.

> [!IMPORTANT]
> No content safety system is infallible and occasional errors can occur, so we recommend integrating supplementary Responsible AI (RAI) tools and practices. For more details, see [Responsible Generative AI Development on Windows](/windows/ai/rai).

## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
