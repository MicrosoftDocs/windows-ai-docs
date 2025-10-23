---
title: Get Started with Text Recognition (OCR) in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) text recognition features that will ship with the Windows App SDK and can be used to identify characters in an image, recognize words, lines, polygonal boundaries, and provide confidence levels for the generated matches.
ms.topic: get-started
ms.date: 10/22/2025
dev_langs:
- csharp
- cpp
---

# Get Started with AI Text Recognition (OCR)

Text recognition, also known as optical character recognition (OCR), is supported in Windows AI Foundry through a set of artificial intelligence (AI)-backed APIs that can detect and extract text within images and convert it into machine readable character streams.

These APIs can identify characters, words, lines, polygonal text boundaries, and provide confidence levels for each match. They are also exclusively supported by hardware acceleration in devices with a neural processing unit (NPU), making them faster and more accurate than the legacy Windows.Media.Ocr.OcrEngine APIs in the [Windows platform SDK](https://developer.microsoft.com/windows/downloads/windows-sdk/).

For **API details**, see [API ref for Text Recognition (OCR)](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

## What can I do with AI Text Recognition?

Use AI Text Recognition features to identify and recognize text in an image. You can also get the text boundaries and confidence scores for the recognized text.

> [!NOTE]
> Characters that are illegible or small in size can generate inaccurate results.

### Create an ImageBuffer from a file

In this WinUI example we call a `LoadImageBufferFromFileAsync` function to get an **ImageBuffer** from an image file.

In the LoadImageBufferFromFileAsync function, we complete the following steps:

1. Create a [StorageFile](/uwp/api/windows.storage.storagefile) object from the specified file path.
1. Open a stream on the StorageFile using [OpenAsync](/uwp/api/windows.storage.storagefile.openasync).
1. Create a [BitmapDecoder](/uwp/api/windows.graphics.imaging.bitmapdecoder) for the stream.
1. Call [GetSoftwareBitmapAsync](/uwp/api/windows.graphics.imaging.bitmapframe.getsoftwarebitmapasync) on the bitmap decoder to get a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object.
1. Return an image buffer from **CreateBufferAttachedToBitmap**.

```csharp
using Microsoft.Windows.AI.Imaging;
using Microsoft.Graphics.Imaging;
using Windows.Graphics.Imaging;
using Windows.Storage;
using Windows.Storage.Streams;

public async Task<ImageBuffer> LoadImageBufferFromFileAsync(string filePath)
{
    StorageFile file = await StorageFile.GetFileFromPathAsync(filePath);
    IRandomAccessStream stream = await file.OpenAsync(FileAccessMode.Read);
    BitmapDecoder decoder = await BitmapDecoder.CreateAsync(stream);
    SoftwareBitmap bitmap = await decoder.GetSoftwareBitmapAsync();

    if (bitmap == null)
    {
        return null;
    }

    return ImageBuffer.CreateBufferAttachedToBitmap(bitmap);
}
```

```cpp
#include <iostream>
#include <sstream>
#include <winrt/Microsoft.Windows.AI.Imaging.h>
#include <winrt/Windows.Graphics.Imaging.h>
#include <winrt/Microsoft.Graphics.Imaging.h>
#include <winrt/Microsoft.UI.Xaml.Controls.h>
#include<winrt/Microsoft.UI.Xaml.Media.h>
#include<winrt/Microsoft.UI.Xaml.Shapes.h>

using namespace winrt;
using namespace Microsoft::UI::Xaml;
using namespace Microsoft::Windows::AI;
using namespace Microsoft::Windows::AI::Imaging;
using namespace winrt::Microsoft::UI::Xaml::Controls;
using namespace winrt::Microsoft::UI::Xaml::Media;


winrt::Windows::Foundation::IAsyncOperation<winrt::hstring> 
    MainWindow::RecognizeTextFromSoftwareBitmap(
        Windows::Graphics::Imaging::SoftwareBitmap const& bitmap)
{
    winrt::Microsoft::Windows::AI::Imaging::TextRecognizer textRecognizer = 
        EnsureModelIsReady().get();
    Microsoft::Graphics::Imaging::ImageBuffer imageBuffer = 
        Microsoft::Graphics::Imaging::ImageBuffer::CreateForSoftwareBitmap(bitmap);
    RecognizedText recognizedText = 
        textRecognizer.RecognizeTextFromImage(imageBuffer);
    std::wstringstream stringStream;
    for (const auto& line : recognizedText.Lines())
    {
        stringStream << line.Text().c_str() << std::endl;
    }
    co_return winrt::hstring{ stringStream.str()};
}
```

### Recognize text in a bitmap image

The following example shows how to recognize some text in a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object as a single string value:

1. Create a **TextRecognizer** object through a call to the `EnsureModelIsReady` function, which also confirms there is a language model present on the system.
1. Using the bitmap obtained in the previous snippet, we call the `RecognizeTextFromSoftwareBitmap` function.
1. Call **CreateBufferAttachedToBitmap** on the image file to get an **ImageBuffer** object.
1. Call **RecognizeTextFromImage** to get the recognized text from the **ImageBuffer**.
1. Create a wstringstream object and load it with the recognized text.
1. Return the string.

> [!NOTE]
> The `EnsureModelIsReady` function is used to check the readiness state of the text recognition model (and install it if necessary).

```csharp
using Microsoft.Windows.AI.Imaging;
using Microsoft.Windows.AI;
using Microsoft.Graphics.Imaging;
using Windows.Graphics.Imaging;
using Windows.Storage;
using Windows.Storage.Streams;

public async Task<string> RecognizeTextFromSoftwareBitmap(SoftwareBitmap bitmap)
{
    TextRecognizer textRecognizer = await EnsureModelIsReady();
    ImageBuffer imageBuffer = ImageBuffer.CreateBufferAttachedToBitmap(bitmap);
    RecognizedText recognizedText = textRecognizer.RecognizeTextFromImage(imageBuffer);
    StringBuilder stringBuilder = new StringBuilder();

    foreach (var line in recognizedText.Lines)
    {
        stringBuilder.AppendLine(line.Text);
    }

    return stringBuilder.ToString();
}

public async Task<TextRecognizer> EnsureModelIsReady()
{
    if (TextRecognizer.GetReadyState() == AIFeatureReadyState.EnsureNeeded)
    {
        var loadResult = await TextRecognizer.EnsureReadyAsync();
        if (loadResult.Status != PackageDeploymentStatus.CompletedSuccess)
        {
            throw new Exception(loadResult.ExtendedError().Message);
        }
    }

    return await TextRecognizer.CreateAsync();
}
```

```cpp
winrt::Windows::Foundation::IAsyncOperation<winrt::Microsoft::Windows::AI::Imaging::TextRecognizer> MainWindow::EnsureModelIsReady()
{
    if (winrt::Microsoft::Windows::AI::Imaging::TextRecognizer::GetReadyState() == AIFeatureReadyState::NotReady)
    {
        auto loadResult = TextRecognizer::EnsureReadyAsync().get();
           
        if (loadResult.Status() != AIFeatureReadyResultState::Success)
        {
            throw winrt::hresult_error(loadResult.ExtendedError());
        }
    }

    return winrt::Microsoft::Windows::AI::Imaging::TextRecognizer::CreateAsync();
}
```

### Get word bounds and confidence

Here we show how to visualize the **BoundingBox** of each word in a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object as a collection of color-coded [polygons](/uwp/api/windows.ui.xaml.shapes.polygon) on a [Grid](/windows/windows-app-sdk/api/winrt/microsoft.ui.xaml.controls.grid) element.

> [!NOTE]
> For this example we assume a **TextRecognizer** object has already been created and passed in to the function.

```csharp
using Microsoft.Windows.AI.Imaging;
using Microsoft.Graphics.Imaging;
using Windows.Graphics.Imaging;
using Windows.Storage;
using Windows.Storage.Streams;

public void VisualizeWordBoundariesOnGrid(
    SoftwareBitmap bitmap,
    Grid grid,
    TextRecognizer textRecognizer)
{
    ImageBuffer imageBuffer = ImageBuffer.CreateBufferAttachedToBitmap(bitmap);
    RecognizedText result = textRecognizer.RecognizeTextFromImage(imageBuffer);

    SolidColorBrush greenBrush = new SolidColorBrush(Microsoft.UI.Colors.Green);
    SolidColorBrush yellowBrush = new SolidColorBrush(Microsoft.UI.Colors.Yellow);
    SolidColorBrush redBrush = new SolidColorBrush(Microsoft.UI.Colors.Red);

    foreach (var line in result.Lines)
    {
        foreach (var word in line.Words)
        {
            PointCollection points = new PointCollection();
            var bounds = word.BoundingBox;
            points.Add(bounds.TopLeft);
            points.Add(bounds.TopRight);
            points.Add(bounds.BottomRight);
            points.Add(bounds.BottomLeft);

            Polygon polygon = new Polygon();
            polygon.Points = points;
            polygon.StrokeThickness = 2;

            if (word.Confidence < 0.33)
            {
                polygon.Stroke = redBrush;
            }
            else if (word.Confidence < 0.67)
            {
                polygon.Stroke = yellowBrush;
            }
            else
            {
                polygon.Stroke = greenBrush;
            }

            grid.Children.Add(polygon);
        }
    }
}
```

```cpp
void MainWindow::VisualizeWordBoundariesOnGrid(
    Windows::Graphics::Imaging::SoftwareBitmap const& bitmap,
    Grid const& grid,
    TextRecognizer const& textRecognizer)
{
    Microsoft::Graphics::Imaging::ImageBuffer imageBuffer = 
        Microsoft::Graphics::Imaging::ImageBuffer::CreateForSoftwareBitmap(bitmap);

    RecognizedText result = textRecognizer.RecognizeTextFromImage(imageBuffer);

    auto greenBrush = SolidColorBrush(winrt::Microsoft::UI::Colors::Green());
    auto yellowBrush = SolidColorBrush(winrt::Microsoft::UI::Colors::Yellow());
    auto redBrush = SolidColorBrush(winrt::Microsoft::UI::Colors::Red());
    for (const auto& line : result.Lines())
    {
        for (const auto& word : line.Words())
        {
            PointCollection points;
            const auto& bounds = word.BoundingBox();
            points.Append(bounds.TopLeft);
            points.Append(bounds.TopRight);
            points.Append(bounds.BottomRight);
            points.Append(bounds.BottomLeft);

            winrt::Microsoft::UI::Xaml::Shapes::Polygon polygon{};
            polygon.Points(points);
            polygon.StrokeThickness(2);
            if (word.MatchConfidence() < 0.33)
            {
                polygon.Stroke(redBrush);
            }
            else if (word.MatchConfidence() < 0.67)
            {
                polygon.Stroke(yellowBrush);
            }
            else
            {
                polygon.Stroke(greenBrush);
            }

            grid.Children().Append(polygon);
        }
    }
}
```

## Responsible AI

We have used a combination of the following steps to ensure these imaging APIs are trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](../rai.md) when implementing AI features in your app.

## See also

- [Access files and folders with Windows App SDK and WinRT APIs](/windows/apps/develop/files/winrt-files)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
