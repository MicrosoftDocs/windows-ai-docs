---
title: Image Description with Windows AI APIs
description: Learn how to use the Image Description API to generate text descriptions for images using the Windows AI APIs.
ms.topic: how-to
ms.date: 11/17/2025
dev_langs:
- csharp
- cpp
---

# Image Description

> [!IMPORTANT]
> Image Description is currently unavailable in China.

You can use the Image Description APIs to generate various types of text descriptions for an image. 

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

For **content moderation details**, see [Content safety with generative AI APIs](content-moderation.md).

[!INCLUDE [AI APIs package manifest requirement](./includes/ai-apis-package-manifest-requirements.md)]

## Description types

The following types of text descriptions are supported:

- [**Brief**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a description suitable for charts and diagrams.
- [**Detailed**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a long description.
- [**Diagram**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a short description suitable for an image caption. The default if no value is specified.
- [**Accessible**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) - Provides a long description with details intended for users with accessibility needs.

## Limitations

Because these APIs use Machine Learning (ML) models, occasional errors can occur where the text does not describe the image correctly. Therefore, we do not recommend using these APIs for images in the following scenarios:

- Where the images contain potentially sensitive content and inaccurate descriptions could be controversial, such as flags, maps, globes, cultural symbols, or religious symbols.
- When accurate descriptions are critical, such as for medical advice or diagnosis, legal content, or financial documents.

## Image Description example

The following example shows how to get a text description for an image based on the specified description type (optional) and level of content moderation (optional).

> [!NOTE]
> The image must be an **ImageBuffer** object as **SoftwareBitmap** is not currently supported (this example demonstrates how to convert **SoftwareBitmap** to **ImageBuffer**).

1. Ensure the Image Description model is available by calling the [GetReadyState](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptiongenerator.getreadystate) method and then waiting for the [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptiongenerator.ensurereadyasync) method to return successfully.

2. Once the Image Description model is available, create an [ImageDescriptionGenerator](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptiongenerator) object to reference it.

3. (Optional) Create a [ContentFilterOptions](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.contentfilteroptions) object and specify your preferred values. If you choose to use default values, you can pass in a null object.

4. Get the image description (**LanguageModelResponse.Response**) by calling the [DescribeAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptiongenerator.describeasync) method specifying the original image, the [ImageDescriptionKind](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagedescriptionkind) (an optional value for the preferred description type), and the [ContentFilterOptions](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.contentfilteroptions) object (optional).

```csharp
using Microsoft.Graphics.Imaging;
using Microsoft.Windows.Management.Deployment;  
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.ContentModeration;
using Windows.Storage.StorageFile;  
using Windows.Storage.Streams;  
using Windows.Graphics.Imaging;

if (ImageDescriptionGenerator.GetReadyState() == AIFeatureReadyState.NotReady) 
{
    var result = await ImageDescriptionGenerator.EnsureReadyAsync();
    if (result.Status != AIFeatureReadyResultState.Success)
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

```cppwinrt
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

    if (loadResult.Status() != AIFeatureReadyResultState::Success)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
}

ImageDescriptionGenerator imageDescriptionGenerator = 
    ImageDescriptionGenerator::CreateAsync().get();

// Convert already available softwareBitmap to ImageBuffer.
auto inputBuffer = Microsoft::Graphics::Imaging::ImageBuffer::CreateForSoftwareBitmap(softwareBitmap);

// Create content moderation thresholds object.

ContentFilterOptions contentFilter{};
contentFilter.PromptMaxAllowedSeverityLevel().Violent(SeverityLevel::Medium);
contentFilter.ResponseMaxAllowedSeverityLevel().Violent(SeverityLevel::Medium);

// Get text description.
auto response = imageDescriptionGenerator.DescribeAsync(inputBuffer, ImageDescriptionKind::BriefDescription, contentFilter).get();
string text = response.Description();
```

## See also

- [AI Imaging overview](imaging.md)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
