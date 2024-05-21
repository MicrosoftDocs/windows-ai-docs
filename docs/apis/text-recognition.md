---
title: Text Recognition in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) text recognition features that will ship with Windows App SDK 1.6 Experimental 2 and can be used to identify characters in an image, recognize words, lines, polygonal boundaries, and provide confidence levels for the generated matches.
ms.topic: article
ms.date: 05/13/2024
ms.author: kbridge
author: karl-bridge-microsoft
---

# Text Recognition in the Windows App SDK

The new Artificial Intelligence (AI) text recognition APIs that will ship with Windows App SDK 1.6 *Experimental 2* can be used to identify characters in an image, recognize words, lines, polygonal boundaries, and provide confidence levels for the generated matches.

These new Windows App SDK APIs are faster and more accurate than the legacy Windows.Media.Ocr.OcrEngine APIs in the Windows platform SDK and support hardware acceleration in devices with a neural processing unit (NPU).

## Prerequisites

- Device with a Neural Processing Unit (NPU).
- Windows App SDK 1.6 *Experimental 2*.

## What can I do with the Windows App SDK and AI Text Recognition?

Use the AI Text Recognition features shipping with the Windows App SDK 1.6 Experimental to identify and recognize text in an image. You can also get the text boundaries and confidence scores for the recognized text.

### Create an ImageBuffer from a file

In this example we call a `LoadImageBufferFromFileAsync` function to get an [ImageBuffer](text-recognition-api-ref.md#microsoftwindowsimagingimagebuffer-class) from an image file.

In the LoadImageBufferFromFileAsync function, we complete the following steps:

1. Create a [StorageFile](/uwp/api/windows.storage.storagefile) object from the specified file path.
1. Open a stream on the StorageFile using [OpenAsync](/uwp/api/windows.storage.storagefile.openasync).
1. Create a [BitmapDecoder](/uwp/api/windows.graphics.imaging.bitmapdecoder) for the stream.
1. Call [GetSoftwareBitmapAsync](/uwp/api/windows.graphics.imaging.bitmapframe.getsoftwarebitmapasync) on the bitmap decoder to get a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object.
1. Return an image buffer from [CreateBufferAttachedToBitmap](text-recognition-api-ref.md#microsoftwindowsimagingimagebuffercreatebufferattachedtobitmapwindowsgraphicsimagingsoftwarebitmap-method).

```cpp
namespace winrt
{
    using namespace Microsoft::Windows::AI::Imaging;
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

This example shows how to recognize some text in a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object as a single string value by following these steps:

1. Create a [TextRecognizer](text-recognition-api-ref.md#microsoftwindowsvisiontextrecognitiontextrecognizer-class) object through a call to the `EnsureModelIsReady` function, which also confirms there is a language model present on the system.
1. Using the bitmap obtained in the previous snippet, we call the `RecognizeTextFromSoftwareBitmap` function.
1. Call [CreateBufferAttachedToBitmap](text-recognition-api-ref.md#microsoftwindowsimagingimagebuffercreatebufferattachedtobitmapwindowsgraphicsimagingsoftwarebitmap-method) on the image file to get an [ImageBuffer](text-recognition-api-ref.md#microsoftwindowsimagingimagebuffer-class) object.
1. Call [RecognizeTextFromImage](text-recognition-api-ref.md#microsoftwindowsvisiontextrecognizerrecognizetextfromimagemicrosoftwindowsimagingimagebuffer-microsoftwindowsvisiontextrecognizeroptions-method) to get the recognized text from the [ImageBuffer](text-recognition-api-ref.md#microsoftwindowsimagingimagebuffer-class).
1. Create a wstringstream object and load it with the recognized text.
1. Return the string.

> [!NOTE]
> The `EnsureModelIsReady` function is used to check the readiness state of the text recognition model (and install it if necessary).

```cpp
namespace winrt
{
    using namespace Microsoft::Windows::Imaging;
    using namespace Microsoft::Windows::Vision;
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
  if (!winrt::TextRecognizer::IsAvailable())
  {
    auto loadResult = co_await winrt::TextRecognizer::MakeAvailableAsync();
    if (loadResult.Status() != winrt::PackageDeploymentStatus::CompletedSuccess)
    {
        throw winrt::hresult_error(loadResult.ExtendedError());
    }
  }

  co_return winrt::TextRecognizer::CreateAsync();
}
```

### Get word bounds and confidence

Here we show how to visualize the [BoundingBox](text-recognition-api-ref.md#microsoftwindowsvisionrecognizedwordboundingbox-property) of each word in a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) object as a collection of color-coded [polygons](/uwp/api/windows.ui.xaml.shapes.polygon) on a [Grid](/windows/windows-app-sdk/api/winrt/microsoft.ui.xaml.controls.grid) element.

> [!NOTE]
> For this example we assume a [TextRecognizer](text-recognition-api-ref.md#microsoftwindowsvisiontextrecognitiontextrecognizer-class) object has already been created and passed in to the function.

```cpp
namespace winrt
{
    using namespace Microsoft::Windows::Imaging;
    using namespace Microsoft::Windows::Vision;
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

<!--
## Get help

If this section is needed, list resources and support services for using the product or service.
-->

## Additional resources

If this section is needed, add links to samples, contextual information about the tasks, or prerequisites in the checklists.

[Access files and folders with Windows App SDK and WinRT APIs](/windows/apps/develop/files/winrt-files)

## Related content

- [API ref for Text Recognition APIs in the Windows App SDK](text-recognition-api-ref.md)
