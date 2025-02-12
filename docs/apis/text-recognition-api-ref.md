---
title: API ref for AI-backed Text Recognition (OCR) in the Windows App SDK
description: Learn about the Windows App SDK APIs, backed by artificial intelligence (AI), that can detect and extract text within images and convert it into machine readable character streams.
ms.topic: article
ms.date: 02/06/2025
---

# API ref for AI Text Recognition (OCR) in the Windows App SDK

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.
>
> - Unpackaged apps are not supported.

Learn about the [Windows App SDK](/windows/apps/windows-app-sdk/) APIs, backed by artificial intelligence (AI), that can detect and extract text (characters, words, lines, polygonal text boundaries, and confidence levels for each match) within images and convert it into machine readable character streams.

For more details, see [Get Started with Text Recognition (OCR) in the Windows App SDK](text-recognition.md).

> [!TIP]
> Provide feedback on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo (include **OCR** in the title) or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

---


<!---
-api-id: N:Microsoft.Windows.Vision
-api-type: winrt namespace
--->

## Microsoft.Windows.Vision namespace

Provides APIs for machine learning models that analyze the textual content of images.


<!---
-api-id: T:Microsoft.Windows.Vision.DetectedLineStyle
-api-type: winrt enum
--->

### DetectedLineStyle enum

```
public enum DetectedLineStyle
```

Specifies the line styles that can be recognized.

#### Fields

##### Handwritten: 0

The line of text is hand written.

##### Other: 1

The line of text is not hand written.


<!---
-api-id: T:Microsoft.Windows.Vision.OrientationDetectionOptions
-api-type: winrt enum
--->

### OrientationDetectionOptions enum

```
public enum OrientationDetectionOptions
```

Specifies the text orientations that can be recognized.

#### Fields

##### None: 0

Orientation is not recognized.

##### DetectOrientation: 1

Orientation is recognized.


<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedLine
-api-type: winrt class
--->

### RecognizedLine class

```
public sealed class RecognizedLine
```

Represents a single line of recognized text.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedLine.Style
-api-type: winrt property
--->

#### RecognizedLine.Style property

```
public Microsoft.Windows.Vision.RecognizedLineStyle Style { get; }
```

Gets the recognized line style.

##### Property value

the recognized line style.

#### Remarks

Includes whether the line of text was handwritten or not and the level of recognition confidence.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedLine.Text
-api-type: winrt property
--->

#### RecognizedLine.Text property

```
public string Text { get; }
```

Gets the text of the recognized line.

##### Property value

The text of the recognized line.

#### Remarks

All words concatenated with spaces.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedLine.Words
-api-type: winrt property
--->

#### RecognizedLine.Words property

```
public Microsoft.Windows.Vision.RecognizedWord[] Words { get; }
```

The words in the recognized line.

##### Property value

The words in the recognized line.



<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedLineStyle
-api-type: winrt struct
--->

### RecognizedLineStyle struct

```
public struct RecognizedLineStyle
```

Represents the style of the recognized line.

#### Fields

##### Confidence

The confidence level of the line style recognition.

##### Name

The line style name.


<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedText
-api-type: winrt class
--->

### RecognizedText class

```
public sealed class RecognizedText
```

Represents the result of an image-to-text recognition operation.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedText.ImageAngle
-api-type: winrt property
--->

#### RecognizedText.ImageAngle property

```
public float ImageAngle { get; }
```

Gets the clockwise rotational angle of the recognized text in degrees.

##### Property value

The clockwise rotational angle of the recognized text in degrees.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedText.Lines
-api-type: winrt property
--->

#### RecognizedText.Lines property

```
public Microsoft.Windows.Vision.RecognizedLine[] Lines { get; }
```

Gets the collection of recognized lines.

##### Property value

The collection of recognized lines.


<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedWord
-api-type: winrt class
--->

### RecognizedWord class

```
public sealed class RecognizedWord
```

Represents a single recognized word.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.BoundingBox
-api-type: winrt property
--->

#### RecognizedWord.BoundingBox property

```
public Microsoft.Windows.Vision.BoundingBox BoundingBox { get; }
```

Gets the quadrilateral boundary of the recognized word.

##### Property value

The quadrilateral boundary of the recognized word. TopLeft is relative to the word's rotation.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.Confidence
-api-type: winrt property
--->

#### RecognizedWord.Confidence property

```
public float Confidence { get; }
```

Gets how likely this word was recognized correctly.

##### Property value

Wow likely this word was recognized correctly. Value ranges from 0.0 to 1.0, inclusive.


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.Text
-api-type: winrt property
--->

#### RecognizedWord.Text property

```
public string Text { get; }
```

Gets the text of the recognized word.

##### Property value

The text of the recognized word.


<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognition.BoundingBox
-api-type: winrt struct
--->

### TextRecognition.BoundingBox struct

```
public struct BoundingBox
```

A polygon with 4 points used for the boundary of recognized words and lines of text.

#### Fields

##### BottomLeft

The bottom left corner of the bounding box.

##### BottomRight

The bottom right corner of the bounding box.

##### TopLeft

The top left point of the bounding box.

##### TopRight

The top right point of the bounding box.

#### Remarks

When returned as a boundary for a word or line, the *TopLeft*, *TopRight*, *BottomRight*, and *BottomLeft* points are relative to the rotation and skew of the recognized text in the image. The following diagram shows the point layout for different text rotations where 0 is *TopLeft*, 1 is *TopRight*, 2 is *BottomRight*, and 3 is *BottomLeft*, all relative to the text.

:::image type="content" source="../images/bounding-box-examples.png" alt-text="Diagram of three bounding box examples showing how corner points are identified based on text rotation.":::


<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognizer
-api-type: winrt class
--->

### TextRecognizer class

```
public sealed class TextRecognizer : System.IDisposable
```

Recognizes words and lines, and their quadrilateral boundaries, in a source image.

<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.Close
-api-type: winrt method
--->

#### TextRecognizer.Close method

<!--
// This member is not implemented in C#
-->

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.

<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.CreateAsync
-api-type: winrt method
--->

#### TextRecognizer.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Windows.Vision.TextRecognizer> CreateAsync ();
```

Asynchronously creates a new instance of the TextRecognizer class.

##### Returns

A new instance of the TextRecognizer class.

This will return an error if GetModelReadyStatus is not Ready.


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.IsAvailable
-api-type: winrt method
--->

#### TextRecognizer.IsAvailable method

```
public static bool IsAvailable ();
```

Retrieves whether the underlying language model is installed.

##### Returns

True if the underlying language model is installed. Otherwise, false.


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.MakeAvailableAsync
-api-type: winrt method
--->

#### TextRecognizer.MakeAvailableAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult, 
Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
```

Ensures the underlying language model is installed and available for use.

##### Returns

An asynchronous action with progress that returns a [PackageDeploymentResult](/windows/windows-app-sdk/api/winrt/microsoft.windows.management.deployment.packagedeploymentresult) on completion.


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImage(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.Vision.TextRecognizerOptions)
-api-type: winrt method
--->

#### TextRecognizer.RecognizeTextFromImage(Microsoft.Graphics.Imaging.ImageBuffer, Microsoft.Windows.Vision.TextRecognizerOptions) method

```
public Microsoft.Windows.Vision.RecognizedText RecognizeTextFromImage (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer, 
Microsoft.Windows.Vision.TextRecognizerOptions options);
```

Recognize text in the provided image.

##### Parameters

###### imageBuffer

An uncompressed bitmap.

###### options

Options for configuring the text recognition model for the TextRecognizer.

##### Returns

The recognized text.


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImageAsync(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.Vision.TextRecognizerOptions)
-api-type: winrt method
--->

#### TextRecognizer.RecognizeTextFromImageAsync(Microsoft.Graphics.Imaging.ImageBuffer, Microsoft.Windows.Vision.TextRecognizerOptions) method

```
public Windows.Foundation.IAsyncOperation<Microsoft.Windows.Vision.RecognizedText> RecognizeTextFromImageAsync (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer, 
Microsoft.Windows.Vision.TextRecognizerOptions options);
```

Asynchronously recognize text in the provided image.

##### Parameters

###### imageBuffer

An uncompressed bitmap.

###### options

Options for configuring the text recognition model for the TextRecognizer.

##### Returns

The recognized text.



<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognizerOptions
-api-type: winrt class
--->

### TextRecognizerOptions class

```
public sealed class TextRecognizerOptions
```

Provides options to configure the text recognition model for a TextRecognizer.



<!---
-api-id: P:Microsoft.Windows.Vision.TextRecognizerOptions.MaxAnalysisSize
-api-type: winrt property
--->

#### TextRecognizerOptions.MaxAnalysisSize property

```
public Windows.Graphics.SizeInt32 MaxAnalysisSize { get; set; }
```

Gets or sets the maximum image size.

##### Property value

The maximum image size. Default value is 1152 width and 768 height.

##### Remarks

This size is a suggestion, and might not always be honored.

If the source image is larger than the maximum size, it will automatically be scaled down to the upper size limits.


<!---
-api-id: P:Microsoft.Windows.Vision.TextRecognizerOptions.MaxLineCount
-api-type: winrt property
--->

#### TextRecognizerOptions.MaxLineCount property

```
public uint MaxLineCount { get; set; }
```

Gets or sets the maximum number of lines to return from the recognition operation.

##### Property value

The maximum number of lines to return from the recognition operation.

##### Remarks

Defaults to MaxLineCountSupported. If specified, the maximum lines returned will be the lesser of this value and MaxLineCountSupported.


<!---
-api-id: P:Microsoft.Windows.Vision.TextRecognizerOptions.OrientationDetection
-api-type: winrt property
--->

#### TextRecognizerOptions.OrientationDetection property

```
public Microsoft.Windows.Vision.OrientationDetectionOptions OrientationDetection { get; set; }
```

Gets or sets whether to detect the text orientation.

##### Property value

Whether to detect the text orientation. Default value is None.


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizerOptions.#ctor
-api-type: winrt constructor
--->

#### TextRecognizerOptions.#ctor constructor

```
public TextRecognizerOptions ();
```

Initializes a new instance of the TextRecognizerOptions class.


## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [Get Started with Text Recognition (OCR) in the Windows App SDK](text-recognition.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
