---
title: Get Started with AI imaging walkthrough
description: Learn about the new Artificial Intelligence (AI) imaging features and walk through tutorials
ms.topic: get-started
ms.date: 08/22/2025
dev_langs:
- csharp
- cpp
---

# Image scaler walkthrough

This short tutorial will walk you through a sample that uses Image Scaler in a .NET MAUI app. To start, ensure you've completed the steps in the [Getting Started page.](get-started.md) for .NET MAUI.

## Introduction

This sample demonstrates use of some Windows AI APIs, including LanguageModel for text generation and ImageScaler for image super resolution to scale and sharpen images. Click one of the "Scale" buttons to scale the image (or reshow the original, unscaled image), or enter a text prompt and click the "Generate" button to generate a text response.

The changes from the ".NET MAUI App" template are split across four files:

1. MauiWindowsAISample.csproj: Adds the required Windows App SDK package reference for the Windows AI APIs. This reference needs to be conditioned only when building for Windows (see Additional Notes below for details). This file also sets the necessary TargetFramework for Windows.
2. Platforms/Windows/MainPage.cs: Implements partial methods from the shared MainPage class to show and handle the text generation and image scaling functionality.
3. MainPage.xaml: Defines controls to show text generation and image scaling.
4. MainPage.xaml.cs: Defines partial methods which the Windows-specific MainPage.cs implements.

In the second file listed above, you'll find the following function, which demonstrates some basic functionality for the ImageScaler method:

```csharp
private async void DoScaleImage(double scale)
{
    // Load the original image
    var resourceManager = new Microsoft.Windows.ApplicationModel.Resources.ResourceManager();
    var resource = resourceManager.MainResourceMap.GetValue("ms-resource:///Files/enhance.png");
    if (resource.Kind == Microsoft.Windows.ApplicationModel.Resources.ResourceCandidateKind.FilePath)
    {
        // Load as a SoftwareBitmap
        var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(resource.ValueAsString);
        var fileStream = await file.OpenStreamForReadAsync();

        var decoder = await BitmapDecoder.CreateAsync(fileStream.AsRandomAccessStream());
        var softwareBitmap = await decoder.GetSoftwareBitmapAsync();
        int origWidth = softwareBitmap.PixelWidth;
        int origHeight = softwareBitmap.PixelHeight;

        SoftwareBitmap finalImage;
        if (scale == 0.0)
        {
            // just show the original image
            finalImage = softwareBitmap;
        }
        else
        {
            // Scale the image to be the exact pixel size of the element displaying it
            if (ImageScaler.GetReadyState() == AIFeatureReadyState.NotReady)
            {
                var op = await ImageScaler.EnsureReadyAsync();
                if (op.Status != AIFeatureReadyResultState.Success)
                {
                    throw new Exception(op.ExtendedError.Message);
                }
            }

            ImageScaler imageScaler = await ImageScaler.CreateAsync();

            double imageScale = scale;
            if (imageScale > imageScaler.MaxSupportedScaleFactor)
            {
                imageScale = imageScaler.MaxSupportedScaleFactor;
            }
            System.Diagnostics.Debug.WriteLine($"Scaling to {imageScale}x...");

            int newHeight = (int)(origHeight * imageScale);
            int newWidth = (int)(origWidth * imageScale);
            finalImage = imageScaler.ScaleSoftwareBitmap(softwareBitmap, newWidth, newHeight);
        }

        // Display the scaled image. The if/else here shows two different approaches to do this.
        var mauiContext = scaledImage.Handler?.MauiContext;
        if (mauiContext != null)
        {
            // set the SoftwareBitmap as the source of the Image control
            var imageToShow = finalImage;
            if (imageToShow.BitmapPixelFormat != BitmapPixelFormat.Bgra8 ||
                imageToShow.BitmapAlphaMode == BitmapAlphaMode.Straight)
            {
                // SoftwareBitmapSource only supports Bgra8 and doesn't support Straight alpha mode, so convert
                imageToShow = SoftwareBitmap.Convert(imageToShow, BitmapPixelFormat.Bgra8, BitmapAlphaMode.Premultiplied);
            }
            var softwareBitmapSource = new SoftwareBitmapSource();
            _ = softwareBitmapSource.SetBitmapAsync(imageToShow);
            var nativeScaledImageView = (Microsoft.UI.Xaml.Controls.Image)scaledImage.ToPlatform(mauiContext);
            nativeScaledImageView.Source = softwareBitmapSource;
        }
        else
        {
            // An alternative approach is to encode the image so a stream can be handed
            // to the Maui ImageSource.

            // Note: There's no "using(...)" here, since this stream needs to be kept alive for the image to be displayed
            var scaledStream = new InMemoryRandomAccessStream();
            {
                BitmapEncoder encoder = await BitmapEncoder.CreateAsync(BitmapEncoder.PngEncoderId, scaledStream);
                encoder.SetSoftwareBitmap(finalImage);
                await encoder.FlushAsync();
                scaledImage.Source = ImageSource.FromStream(() => scaledStream.AsStream());
            }
        }
    }
}
```

## Build and run the sample

1. Clone the [WindowsAppSDK-Samples](https://github.com/microsoft/WindowsAppSDK-Samples) repo.
1. Switch to the "release/experimental" branch.
1. Navigate to the [Samples/WindowsAIFoundry/cs-maui](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry/cs-maui) folder.
1. Open MauiWindowsAISample.sln in Visual Studio 2022.
1. Ensure the debug toolbar has "Windows Machine" set as the target device.
1. Press F5 or select "Start Debugging" from the Debug menu to run the sample (the sample can also be run without debugging by selecting "Start Without Debugging" from the Debug menu or Ctrl+F5).

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
