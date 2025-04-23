---
title: Get Started with Text Recognition (OCR) in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) text recognition features that will ship with the Windows App SDK and can be used to identify characters in an image, recognize words, lines, polygonal boundaries, and provide confidence levels for the generated matches.
ms.topic: article
ms.date: 02/06/2025
dev_langs:
- csharp
- cpp
---

## Get Started with AI Text Recognition (OCR)

Text recognition, also known as optical character recognition (OCR), is supported in Windows Copilot Runtime through a set of artificial intelligence (AI)-backed APIs that can detect and extract text within images and convert it into machine readable character streams.

These APIs can identify characters, words, lines, polygonal text boundaries, and provide confidence levels for each match. They are also exclusively supported by hardware acceleration in in devices with a neural processing unit (NPU), making them faster and more accurate than the legacy Windows.Media.Ocr.OcrEngine APIs in the [Windows platform SDK](https://developer.microsoft.com/windows/downloads/windows-sdk/).

For **API details**, see [API ref for Text Recognition (OCR)](text-recognition-api-ref.md).

## What can I do with AI Text Recognition?

Use AI Text Recognition features to identify and recognize text in an image. You can also get the text boundaries and confidence scores for the recognized text.

### Create an ImageBuffer from a file

In this example we call a `LoadImageBufferFromFileAsync` function to get an [ImageBuffer](imaging-api-ref.md#imagebuffer-class) from an image file.

In the LoadImageBufferFromFileAsync function, we complete the following steps:

1. Create a [StorageFile](/uwp/api/windows.storage.storagefile) object from the specified file path.
1. Open a stream on the StorageFile using [OpenAsync](/uwp/api/windows.storage.storagefile.openasync).
1. Create a [BitmapDecoder](/uwp/api/windows.graphics.imaging.bitmapdecoder) for the stream.
1. Call [GetSoftwareBitmapAsync](/uwp/api/windows.graphics.imaging.bitmapframe.getsoftwarebitmapasync) on the bitmap decoder to get a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object.
1. Return an image buffer from [CreateBufferAttachedToBitmap](imaging-api-ref.md#imagebuffercreatebufferattachedtobitmapwindowsgraphicsimagingsoftwarebitmap-method).

```csharp
using Microsoft.Windows.Vision;
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
namespace winrt
{
    using namespace Microsoft::Windows::Vision;
    using namespace Microsoft::Windows::Imaging;
    using namespace Windows::Graphics::Imaging;
    using namespace Windows::Storage;
    using namespace Windows::Storage::Streams;
}

winrt::IAsyncOperation<winrt::ImageBuffer> LoadImageBufferFromFileAsync(
    const std::wstring& filePath)
{
    auto file = co_await winrt::StorageFile::GetFileFromPathAsync(filePath);
    auto stream = co_await file.OpenAsync(winrt::FileAccessMode::Read);
    auto decoder = co_await winrt::BitmapDecoder::CreateAsync(stream);
    auto bitmap = co_await decoder.GetSoftwareBitmapAsync();
    if (bitmap == nullptr) {
        co_return nullptr;
    }
    co_return winrt::ImageBuffer::CreateBufferAttachedToBitmap(bitmap);
}
```

### Recognize text in a bitmap image

The following example shows how to recognize some text in a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object as a single string value:

1. Create a [TextRecognizer](text-recognition-api-ref.md#textrecognizer-class) object through a call to the `EnsureModelIsReady` function, which also confirms there is a language model present on the system.
1. Using the bitmap obtained in the previous snippet, we call the `RecognizeTextFromSoftwareBitmap` function.
1. Call [CreateBufferAttachedToBitmap](imaging-api-ref.md#imagebuffercreatebufferattachedtobitmapwindowsgraphicsimagingsoftwarebitmap-method) on the image file to get an [ImageBuffer](imaging-api-ref.md#imagebuffer-class) object.
1. Call [RecognizeTextFromImage](text-recognition-api-ref.md#textrecognizerrecognizetextfromimagemicrosoftgraphicsimagingimagebuffer-microsoftwindowsvisiontextrecognizeroptions-method) to get the recognized text from the [ImageBuffer](imaging-api-ref.md#imagebuffer-class).
1. Create a wstringstream object and load it with the recognized text.
1. Return the string.

> [!NOTE]
> The `EnsureModelIsReady` function is used to check the readiness state of the text recognition model (and install it if necessary).

```csharp
using Microsoft.Windows.Vision;
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
    if (!TextRecognizer.GetReadyState())
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
namespace winrt
{
    using namespace Microsoft::Windows::Vision;
    using namespace Microsoft::Windows::Imaging;
    using namespace Windows::Graphics::Imaging;
}

winrt::IAsyncOperation<winrt::TextRecognizer> EnsureModelIsReady();

winrt::IAsyncOperation<winrt::hstring> RecognizeTextFromSoftwareBitmap(winrt::SoftwareBitmap const& bitmap)
{
    winrt::TextRecognizer textRecognizer = co_await EnsureModelIsReady();
    winrt::ImageBuffer imageBuffer = winrt::ImageBuffer::CreateBufferAttachedToBitmap(bitmap);
    winrt::RecognizedText recognizedText = textRecognizer.RecognizeTextFromImage(imageBuffer);
    std::wstringstream stringStream;
    for (const auto& line : recognizedText.Lines())
    {
        stringStream << line.Text().c_str() << std::endl;
    }
    co_return winrt::hstring{stringStream.view()};
}

winrt::IAsyncOperation<winrt::TextRecognizer> EnsureModelIsReady()
{
  if (!winrt::TextRecognizer::GetReadyState())
  {
    auto loadResult = co_await winrt::TextRecognizer::EnsureReadyAsync();
    if (loadResult.Status() != winrt::PackageDeploymentStatus::CompletedSuccess)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
  }

  co_return winrt::TextRecognizer::CreateAsync();
}
```

### Get word bounds and confidence

Here we show how to visualize the [BoundingBox](text-recognition-api-ref.md#recognizedwordboundingbox-property) of each word in a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object as a collection of color-coded [polygons](/uwp/api/windows.ui.xaml.shapes.polygon) on a [Grid](/windows/windows-app-sdk/api/winrt/microsoft.ui.xaml.controls.grid) element.

> [!NOTE]
> For this example we assume a [TextRecognizer](text-recognition-api-ref.md#textrecognizer-class) object has already been created and passed in to the function.

```csharp
using Microsoft.Windows.Vision;
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
namespace winrt
{
    using namespace Microsoft::Windows::Vision;
    using namespace Microsoft::Windows::Imaging;
    using namespace Micrsooft::Windows::UI::Xaml::Controls;
    using namespace Micrsooft::Windows::UI::Xaml::Media;
    using namespace Micrsooft::Windows::UI::Xaml::Shapes;
}

void VisualizeWordBoundariesOnGrid(
    winrt::SoftwareBitmap const& bitmap,
    winrt::Grid const& grid,
    winrt::TextRecognizer const& textRecognizer)
{
    winrt::ImageBuffer imageBuffer = winrt::ImageBuffer::CreateBufferAttachedToBitmap(bitmap);
    
    winrt::RecognizedText result = textRecognizer.RecognizeTextFromImage(imageBuffer);

    auto greenBrush = winrt::SolidColorBrush(winrt::Microsoft::UI::Colors::Green);
    auto yellowBrush = winrt::SolidColorBrush(winrt::Microsoft::UI::Colors::Yellow);
    auto redBrush = winrt::SolidColorBrush(winrt::Microsoft::UI::Colors::Red);
    
    for (const auto& line : recognizedText.Lines())
    {
        for (const auto& word : line.Words())
        {
            winrt::PointCollection points;
            const auto& bounds = word.BoundingBox();
            points.Append(bounds.TopLeft);
            points.Append(bounds.TopRight);
            points.Append(bounds.BottomRight);
            points.Append(bounds.BottomLeft);

            winrt::Polygon polygon;
            polygon.Points(points);
            polygon.StrokeThickness(2);

            if (word.Confidence() < 0.33)
            {
                polygon.Stroke(redBrush);
            }
            else if (word.Confidence() < 0.67)
            {
                polygon.Stroke(yellowBrush);
            }
            else
            {
                polygon.Stroke(greenBrush);
            }

            grid.Children().Add(polygon);
        }
    }
}
```

## Responsible AI

We have used a combination of the following steps to ensure these imaging APIs are trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](../rai.md) when implementing AI features in your app.

## Additional resources

[Access files and folders with Windows App SDK and WinRT APIs](/windows/apps/develop/files/winrt-files)
