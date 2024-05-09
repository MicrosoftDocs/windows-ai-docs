---
title: Get started with ONNX models in your WinUI app with ONNX Runtime
description: TODO Description needed.
ms.author: drewbat
author: drewbatgit
ms.date: 05/21/2024
ms.topic: article
#customer intent: As a <role>, I want <what> so that <why>.
---

# Get started with ONNX models in your WinUI app with ONNX Runtime

This article walks you through creating a WinUI 3 app that uses an ONNX model to classify objects in an image and display the confidence of each classification.


## What is the ONNX runtime

ONNX Runtime is a cross-platform machine-learning model accelerator, with a flexible interface to integrate hardware-specific libraries. ONNX Runtime can be used with models from PyTorch, Tensorflow/Keras, TFLite, scikit-learn, and other frameworks. For more information, see the ONNX Runtime website at [https://onnxruntime.ai/docs/](https://onnxruntime.ai/docs/).


## Prerequisites

- Your device must have developer mode enabled. For more information see [Enable your device for development](/windows/apps/get-started/enable-your-device-for-development).
- Visual Studio 2022 or later with the [TBD - required workloads / components] workload.

## Create a new C# WinUI app

In Visual Studio, create a new project. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "winui", then select the **Blank app, Packaged (WinUI3 in Desktop)** template. Name the new project "ONNXWinUIExample".

## Add references to Nuget packages

In **Solution Explorer**, right-click **Dependencies** and select **Manage NuGet packages...**. In the NuGet package manager, select the **Browse** tab. Search for the following package and for each, select the latest stable version in the **Version** drop-down and then click **Install**.


| Package | Description |
|---------|-------------|
| Microsoft.ML.OnnxRuntime.DirectML | Provides APIs for running ONNX models on the GPU. |
| SixLabors.ImageSharp | Provides image utilities for processing images for model input. |
| SharpDX.DXGI | Provides APIs for accessing the DirectX device from C#. |



## Add the model to your project

In **Solution Explorer**, right-click your project and select **Add->New Folder**. Name the new folder "model". For this example, we will be using the **resnet50-v2-7.onnx** model from [https://github.com/onnx/models](https://github.com/onnx/models). Go to the repo view for the model at [https://github.com/onnx/models/blob/main/validated/vision/classification/resnet/model/resnet50-v2-7.onnx](https://github.com/onnx/models/blob/main/validated/vision/classification/resnet/model/resnet50-v2-7.onnx). Click the **Download raw file* button. Copy this file into the "model" directory you just created. 

In Solution Explorer, click on the model file and set **Copy to Output Directory** to "Copy if Newer".


## Create a simple UI

For this example, we will create a simple UI that includes a [Button](/windows/windows-app-sdk/api/winrt/microsoft.ui.xaml.controls.button) to allow the user to select an image to evaluate with the model, an [Image](/windows/windows-app-sdk/api/winrt/microsoft.ui.xaml.controls.image) control to display the selected image, and a [TextBlock](/windows/windows-app-sdk/api/winrt/microsoft.ui.xaml.controls.textblock) to list the objects the model detected in the image and the confidence of each object classification.

In the `MainWindow.xaml` file, replace the default **StackPanel** element with the following XAML code.

```xaml
<!--MainWindow.xaml-->
<Grid Padding="25" >
    <Grid.ColumnDefinitions>
        <ColumnDefinition/>
        <ColumnDefinition/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
    <Button x:Name="myButton" Click="myButton_Click" Grid.Column="0" VerticalAlignment="Top">Select photo</Button>
    <Image x:Name="myImage" MaxWidth="300" Grid.Column="1" VerticalAlignment="Top"/>
    <TextBlock x:Name="featuresTextBlock" Grid.Column="2" VerticalAlignment="Top"/>
</Grid>
```



## Initialize the model

In the `MainWindow.xaml.cs` file, inside the **MainWindow** class, create a helper method called **InitModel** that will initialize the model. This method uses APIs from the **SharpDX.DXGI** library to loop over the graphics adapters on the device to find the device with the most dedicated video memory. The selected adapter is set in the [SessionOptions](https://onnxruntime.ai/docs/api/csharp/api/Microsoft.ML.OnnxRuntime.SessionOptions.html) object as the execution provider for the session. Finally, a new [InferenceSession](https://onnxruntime.ai/docs/api/csharp/api/Microsoft.ML.OnnxRuntime.InferenceSession.html) is initialized, passing in the path to the model file and the session options.

```csharp
// MainWindow.xaml.cs

private InferenceSession _inferenceSession;
private string modelDir = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "model");

private void InitModel()
{
    if (_inferenceSession != null)
    {
        return;
    }

    // Select a graphics device
    var factory1 = new Factory1();
    int deviceId = 0;
    Adapter1 selectedAdapter = null;

    for (int i = 0; i < factory1.GetAdapterCount1(); i++)
    {
        Adapter1 adapter = factory1.GetAdapter1(i);
        Debug.WriteLine($"Adapter {i}:");
        Debug.WriteLine($"\tDescription: {adapter.Description1.Description}");
        Debug.WriteLine($"\tDedicatedVideoMemory: {(long)adapter.Description1.DedicatedVideoMemory / 1000000000}GB");
        if (selectedAdapter == null || (long)adapter.Description1.DedicatedVideoMemory > (long)selectedAdapter.Description1.DedicatedVideoMemory)
        {
            selectedAdapter = adapter;
            deviceId = i;
        }
    }

    // Create the inference session
    var sessionOptions = new SessionOptions
    {
        LogSeverityLevel = OrtLoggingLevel.ORT_LOGGING_LEVEL_INFO
    };
    sessionOptions.AppendExecutionProvider_DML(deviceId);
    _inferenceSession = new InferenceSession($@"{modelDir}\resnet50-v2-7.onnx", sessionOptions);

}
```

## Load and analyze an image

For simplicity, for this example all of the steps for loading and formatting the image, invoking the model, and displaying the results will be placed within the button click handler.

```csharp
// MainWindow.xaml.cs

private async void myButton_Click(object sender, RoutedEventArgs e)
{
    ...
}
```

Use a **FileOpenPicker** to allow the user to select an image from their computer to analyze and display it in the UI.

```csharp
    FileOpenPicker fileOpenPicker = new()
    {
        ViewMode = PickerViewMode.Thumbnail,
        FileTypeFilter = { ".jpg", ".jpeg", ".png", ".gif" },
    };
    InitializeWithWindow.Initialize(fileOpenPicker, WinRT.Interop.WindowNative.GetWindowHandle(this));
    StorageFile file = await fileOpenPicker.PickSingleFileAsync();
    if (file == null)
    {
        return;
    }

    // Display the image in the UI
    var bitmap = new BitmapImage();
    bitmap.SetSource(await file.OpenAsync(Windows.Storage.FileAccessMode.Read));
    myImage.Source = bitmap;
```

Next we need to process the input to get it into a format that is supported by the model. The **SixLabors.ImageSharp** library is used to load the image in 24-bit RGB format and resize the image to 224x224 pixels. Then the pixel values are normalized are normalized with a mean of 255*[0.485, 0.456, 0.406] and standard deviation of 255*[0.229, 0.224, 0.225]. The details of the format the model expects can be found on the github page the [resnet model](https://github.com/onnx/models/tree/main/validated/vision/classification/resnet#preprocessing).

```csharp
    using var fileStream = await file.OpenStreamForReadAsync();

    IImageFormat format = SixLabors.ImageSharp.Image.DetectFormat(fileStream);
    using Image<Rgb24> image = SixLabors.ImageSharp.Image.Load<Rgb24>(fileStream);


    // Resize image
    using Stream imageStream = new MemoryStream();
    image.Mutate(x =>
    {
        x.Resize(new ResizeOptions
        {
            Size = new SixLabors.ImageSharp.Size(224, 224),
            Mode = ResizeMode.Crop
        });
    });

    image.Save(imageStream, format);

    // Prepocess image
    // We use DenseTensor for multi-dimensional access to populate the image data
    var mean = new[] { 0.485f, 0.456f, 0.406f };
    var stddev = new[] { 0.229f, 0.224f, 0.225f };
    DenseTensor<float> processedImage = new(new[] { 1, 3, 224, 224 });
    image.ProcessPixelRows(accessor =>
    {
        for (int y = 0; y < accessor.Height; y++)
        {
            Span<Rgb24> pixelSpan = accessor.GetRowSpan(y);
            for (int x = 0; x < accessor.Width; x++)
            {
                processedImage[0, 0, y, x] = ((pixelSpan[x].R / 255f) - mean[0]) / stddev[0];
                processedImage[0, 1, y, x] = ((pixelSpan[x].G / 255f) - mean[1]) / stddev[1];
                processedImage[0, 2, y, x] = ((pixelSpan[x].B / 255f) - mean[2]) / stddev[2];
            }
        }
    });
```

Next, we set up the inputs by creating an [OrtValue](https://onnxruntime.ai/docs/api/csharp/api/Microsoft.ML.OnnxRuntime.OrtValue.html) of Tensor type on top of the managed image data array.

```csharp
    // Setup inputs
    // Pin tensor buffer and create a OrtValue with native tensor that makes use of
    // DenseTensor buffer directly. This avoids extra data copy within OnnxRuntime.
    // It will be unpinned on ortValue disposal
    using var inputOrtValue = OrtValue.CreateTensorValueFromMemory(OrtMemoryInfo.DefaultInstance,
        processedImage.Buffer, new long[] { 1, 3, 224, 224 });

    var inputs = new Dictionary<string, OrtValue>
    {
        { "data", inputOrtValue }
    };
```

Next, if the inference session hasn't been initialized yet, call out **InitModel** helper method. Then call the **Run** method to run the model and retrieve the results.

```csharp
    // Run inference
    if (_inferenceSession == null)
    {
        InitModel();
    }
    using var runOptions = new RunOptions();
    using IDisposableReadOnlyCollection<OrtValue> results = _inferenceSession.Run(runOptions, inputs, _inferenceSession.OutputNames);
```

The model outputs the results as a native tensor buffer. The following code converts the output into an array of floats. A softmax function is applied so that the values lie in the range [0,1] and sum to 1.

```csharp
    // Postprocess output
    // We copy results to array only to apply algorithms, otherwise data can be accessed directly
    // from the native buffer via ReadOnlySpan<T> or Span<T>
    var output = results[0].GetTensorDataAsSpan<float>().ToArray();
    float sum = output.Sum(x => (float)Math.Exp(x));
    IEnumerable<float> softmax = output.Select(x => (float)Math.Exp(x) / sum);
```

The index of each value in the output array maps to a label that the model was trained on, and the value at that index is the model's confidence that the label represents an object detected in the input image. We pick the 10 results with the highest confidence value. This code uses some helper objects that we will define in the next step.

```csharp
    // Extract top 10
    IEnumerable<Prediction> top10 = softmax.Select((x, i) => new Prediction { Label = LabelMap.Labels[i], Confidence = x })
        .OrderByDescending(x => x.Confidence)
        .Take(10);

    // Print results
    featuresTextBlock.Text = "Top 10 predictions for ResNet50 v2...\n";
    featuresTextBlock.Text += "-------------------------------------\n";
    foreach (var t in top10)
    {
        featuresTextBlock.Text += $"Label: {t.Label}, Confidence: {t.Confidence}\n";
    }
}
```


### Declare helper objects

The **Prediction** class just provides a simple way to associate an object label with a confidence value. In `MainPage.xaml.cs`, add this class inside the **ONNXWinUIExample** namespace block, but outside of the **MainWindow** class definition.

```csharp
internal class Prediction
{
    public object Label { get; set; }
    public float Confidence { get; set; }
}
```

Next add the **LabelMap** helper class that lists all of the object labels the model was trained on, in a specific order so that the labels map to the indices of the results returned by the model. The list of labels is too long to present in full here. You can copy the complete **LabelMap** class from a sample code file in the [ONNXRuntime github repo][https://github.com/microsoft/onnxruntime/blob/main/csharp/sample/Microsoft.ML.OnnxRuntime.ResNet50v2Sample/LabelMap.cs] and paste it into the **ONNXWinUIExample** namespace block.

```csharp
public class LabelMap
{
    public static readonly string[] Labels = new[] {
        "tench",
        "goldfish",
        "great white shark",
        ...
        "hen-of-the-woods",
        "bolete",
        "ear",
        "toilet paper"};
```

## Run the example

Build and run the project. Click the **Select photo** button and pick an image file to analyze. You can look at the **LabelMap** helper class definition to see the things the model can recognize and pick an image that might have interesting results. After the model initializes, the first time it is run, and after the model's processing is complete, you should see a list of objects that were detected in image, and the confidence value of each prediction.

`Top 10 predictions for ResNet50 v2...`
`-------------------------------------`
`Label: lakeshore, Confidence: 0.91674984`
`Label: seashore, Confidence: 0.033412453`
`Label: promontory, Confidence: 0.008877817`
`Label: shoal, Confidence: 0.0046836217`
`Label: container ship, Confidence: 0.001940886`
`Label: Lakeland Terrier, Confidence: 0.0016400366`
`Label: maze, Confidence: 0.0012478716`
`Label: breakwater, Confidence: 0.0012336193`
`Label: ocean liner, Confidence: 0.0011933135`
`Label: pier, Confidence: 0.0011284945`

## Next steps

[TBD - Next steps]

## See also

[TBD - See also]
