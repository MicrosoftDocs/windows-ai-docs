---
title: Get Started with AI Image Generation in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) image generation features that ship with the Windows App SDK and can be used to to create, transform, and enhance images and photos using natural language prompts and on-device generative models.
ms.topic: get-started
ms.date: 11/18/2025
dev_langs:
- csharp
---

# Get started with AI Image Generation

AI Image Generation features are supported by Microsoft Foundry on Windows through a set of artificial intelligence-backed, Stable Diffusion-powered (open-source AI model used for processing images) APIs that ship in the Windows App SDK. These APIs can be used in your Windows apps to create, transform, and enhance images and photos using natural language prompts and on-device generative models.

AI Image Generation is optimized for efficiency and performance on Windows Copilot+ PCs.

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

## Prerequisites

- **Windows version:** Windows 11, version 24H2 (build 26100) or later
- **WinAppSDK version:** [Version 2.0 Experimental (2.0.0-Experimental3)](/windows/apps/windows-app-sdk/experimental-channel#version-20-experimental-200-experimental3)
- **Hardware:** NPU-enabled PC recommended  

## What can I do with AI Image Generation?

Use AI Image Generation to turn prompts into visual artifacts. Supported features include:

- **Text-to-Image**

  Generate images from descriptive text prompts. Useful for illustrations, design, customized backgrounds, and conceptual visualization.

- **Image-to-Image**

  Transform an existing image based on textual guidance while preserving structure. Useful for styling, theming, and other variations.

- **Magic Fill**

  Fill masked regions of an image with AI-generated content. Useful for removing objects, repairing regions, and intuitive editing (complex revisions through text prompts instead of manual tools).

- **Coloring Book Style**

  Convert images into simplified outlines that can be used for a coloring book or similar educational experience.

- **Restyle**

  Apply artistic or visual styles to existing images while preserving structure. Useful for creative filters, artistic modes, or themed transformations.

## Examples

Follow these basic steps when using the AI Image Generation APIs.

1. Ensure the model is ready using [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagegenerator.ensurereadyasync).  
2. Create an [ImageGenerator](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging.imagegenerator) instance.
3. Select the appropriate generation workflow (text prompt, image input, or mask).  
4. Invoke the corresponding generation method.  
5. Receive the output as an [ImageBuffer](/windows/windows-app-sdk/api/winrt/microsoft.graphics.imaging.imagebuffer) for viewing, editing, or saving.

### Generate an image from a text prompt (Text-to-Image)

This example shows how to generate an image from a text prompt. Specifically, "A beautiful sunset over a mountain lake".

```csharp
using Microsoft.Windows.AI.Imaging;
using Microsoft.Graphics.Imaging;

public async Task GenerateImageFromText()
{
    // Check if models are ready
    var readyState = ImageGenerator.GetReadyState();
    if (readyState != AIFeatureReadyState.Ready)
    {
        // Download models if needed
        var result = await ImageGenerator.EnsureReadyAsync();
        if (result.Status != AIFeatureReadyResultState.Success)
        {
            Console.WriteLine("Failed to prepare models");
            return;
        }
    }

    // Create ImageGenerator instance
    using var generator = await ImageGenerator.CreateAsync();
    
    // Configure generation options
    var options = new ImageGenerationOptions
    {
        MaxInferenceSteps = 6,
        Creativity = 0.8,
        Seed = 42
    };

    // Generate image
    var result = generator.GenerateImageFromTextPrompt("A beautiful sunset over a mountain lake", options);
    
    if (result.Status == ImageGeneratorResultStatus.Success)
    {
        var imageBuffer = result.Image;
        // Use the generated image (save to file, display, etc.)
        await SaveImageBufferAsync(imageBuffer, "generated_image.png");
    }
    else
    {
        Console.WriteLine($"Image generation failed: {result.Status}");
    }
}
```

### Transform an image style (Image-to-Image)

This example shows how to transform a photograph into an oil painting based on a text prompt. Specifically, "oil painting style, thick brush strokes, artistic".

```csharp
public async Task RestyleImage()
{
    using var generator = await ImageGenerator.CreateAsync();
    
    // Load input image
    var inputImage = await LoadImageBufferAsync("photo.jpg");
    
    var options = new ImageGenerationOptions();
    var styleOptions = new ImageFromImageGenerationOptions
    {
        Style = ImageFromImageGenerationStyle.Restyle,
        ColorPreservation = 0.7f
    };

    var result = generator.GenerateImageFromImageBuffer(
        inputImage, 
        "oil painting style, thick brush strokes, artistic", 
        options, 
        styleOptions);
    
    if (result.Status == ImageGeneratorResultStatus.Success)
    {
        await SaveImageBufferAsync(result.Image, "restyled_image.png");
    }
}
```

### Transform an image style (Image-to-Image complex)

This example shows how to transform a photograph into an oil painting based on a text prompt. Specifically, "An oil painting, thick brush strokes, rich color palette, traditional canvas texture, realistic lighting, classical fine art style, layered paint, high detail, dramatic contrast, impasto, textured canvas".

```c#
using Microsoft.Windows.AI.Imaging;

public async Task CreateImageFromPrompt()
{
    using ImageGenerator model = await ImageGenerator.CreateAsync();

    // Using default values
    var options = new ImageGenerationOptions();

    // Set ImageFromImageGenerationOptions fields
    var imageFromImageOptions = new ImageFromImageGenerationOptions();
    imageFromImageOptions.Style = ImageFromImageGenerationStyle.Restyle;
    imageFromImageOptions.ColorPreservation = 0.5f; // range [0.0f, 1.0f]

    // Load an input image buffer
    using var inputImage = await Utils.LoadSampleImageBufferAsync("sdxl_input_horse.png");

    var textPrompt = "An oil painting, thick brush strokes, rich color palette, traditional canvas texture, realistic lighting, classical fine art style, layered paint, high detail, dramatic contrast, impasto, textured canvas";

    var result = model.GenerateImageFromImageBuffer(inputImage, textPrompt, options, imageFromImageOptions);
    if (result.Status == ImageGeneratorResultStatus.Success)
    {
        // Image generated successfully
        var imageBuffer = result.Image;
        // Process the imageBuffer as needed, e.g., save to file or display
    }
    else
    {
        // Handle error cases based on result.Status
        Console.WriteLine($"Image generation failed with status: {result.Status}");
    }
}
```

### Magic Fill with Mask

This example shows how to use a mask to fill a region of an image. Specifically, "a red sports car".

```csharp
public async Task FillMaskedRegion()
{
    using var generator = await ImageGenerator.CreateAsync();
    
    var inputImage = await LoadImageBufferAsync("scene.jpg");
    var maskImage = await LoadImageBufferAsync("mask.png"); // GRAY8 format
    
    var options = new ImageGenerationOptions();
    
    var result = generator.GenerateImageFromImageBufferAndMask(
        inputImage, 
        maskImage, 
        "a red sports car", 
        options);
    
    if (result.Status == ImageGeneratorResultStatus.Success)
    {
        await SaveImageBufferAsync(result.Image, "filled_image.png");
    }
}
```

### Generate coloring-book style image

This example shows how to generate an image in a coloring book style. Specifically, a "Cat in spaceship".

```c#
using Microsoft.Windows.AI.Imaging;

public async Task CreateImageFromPrompt()
{
    using ImageGenerator model = await ImageGenerator.CreateAsync();

    // Using default values
    var options = new ImageGenerationOptions();

    // Set ImageFromTextGenerationOptions fields
    var imageFromTextOptions = new ImageFromTextGenerationOptions();
    imageFromTextOptions.Style = ImageFromTextGenerationStyle.ColoringBook;

    var result = model.GenerateImageFromTextPrompt("Cat in spaceship", options, imageFromTextOptions);
    if (result.Status == ImageGeneratorResultStatus.Success)
    {
        // Image generated successfully
        var imageBuffer = result.Image;
        // Process the imageBuffer as needed, e.g., save to file or display
    }
    else
    {
        // Handle error cases based on result.Status
        Console.WriteLine($"Image generation failed with status: {result.Status}");
    }
}
```

### Generate image using custom ImageGenerationOptions parameters

This example shows how to generate an image based on a set of content filters and restrictions. Specifically, a "Cat in spaceship" using a [TextContentFilterSeverity](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.textcontentfilterseverity.-ctor#microsoft-windows-ai-contentsafety-textcontentfilterseverity-ctor(microsoft-windows-ai-contentsafety-severitylevel)) of [Low](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.severitylevel) and an [ImageContentFilterSeverity](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.imagecontentfilterseverity.-ctor#microsoft-windows-ai-contentsafety-imagecontentfilterseverity-ctor(microsoft-windows-ai-contentsafety-severitylevel)) of [Minimum](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.severitylevel).

```c#
using Microsoft.Windows.AI.Imaging;
using Microsoft.Windows.AI.ContentSafety;

public async Task CreateImageFromPromptAndCustomOptions()
{
    using ImageGenerator model = await ImageGenerator.CreateAsync();

    // Using default values
    var options = new ImageGenerationOptions();

    // Set custom ImageGenerationOptions fields
    options.MaxInferenceSteps = 6;
    options.Creativity = 0.8;
    options.Seed = 1234;
    ContentFilterOptions contentFilterOptions = new ContentFilterOptions();
    contentFilterOptions.PromptMaxAllowedSeverityLevel = TextContentFilterSeverity(SeverityLevel.Low);
    contentFilterOptions.ImageMaxAllowedSeverityLevel = ImageContentFilterSeverity(SeverityLevel.Minimium);
    options.ContentFilterOptions = contentFilterOptions;

    var result = model.GenerateImageFromTextPrompt("Cat in spaceship", options);
    if (result.Status == ImageGeneratorResultStatus.Success)
    {
        // Image generated successfully
        var imageBuffer = result.Image;
        // Process the imageBuffer as needed, e.g., save to file or display
    }
    else
    {
        // Handle error cases based on result.Status
        Console.WriteLine($"Image generation failed with status: {result.Status}");
    }
}
```

## Responsible AI

We have used a combination of the following steps to ensure these imaging APIs are trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](/windows/ai/rai) when implementing AI features in your app.

## See also

- [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging)