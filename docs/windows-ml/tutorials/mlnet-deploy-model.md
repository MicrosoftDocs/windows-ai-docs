---
title: Deploy your ML.NET model in a Windows app with Windows ML API
description: Deploy your ML.NET image classification model in a Windows App
ms.date: 5/7/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials
ms.localizationpriority: medium
---

# Deploy your ML.NET model in a Windows app with the Windows Machine Learning APIs

[In the previous part of this tutorial](mlnet-train-model.md), you learned how to build and export a ML.NET model in ONNX format. Now that you have that model, you can embed it into a Windows application and run it locally on a device by calling WinML APIs. 

Once we're done, you'll have a working Image classifier WinML UWP app (C#). 

## About the sample app

Using our model, we'll create an app that can classify images of food. It allows you to select an image from your local device and process it by a locally stored classification ONNX model you built and trained [in the previous part](mlnet-train-model.md). The tags returned are displayed next to the image, as well as the the confidence probability of the classification.

If you've been following this tutorial so far, you should already have the necessary prerequisites for app development in place. If you need a refresher, see [the first part of this tutorial](mlnet-intro.md).

> [!NOTE]
> If you prefer to download the complete sample code, you can [download the solution file here](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/Tutorial%20Samples/MLNET%20and%20Windows%20ML). Download a local copy of the repo, navigate to this sample, then open the `classifierMLNETModel.sln` file with Visual Studio. Then, you can skip to the [Launch the application](#Launch the application) step.


## Create a WinML UWP (C#) 

Below, we'll show you how to create your app and WinML code from scratch. You'll learn how to:

*	Load a machine learning model.
*	Load an image in the required format.
*	Bind the model's inputs and outputs.
*	Evaluate the model and display meaningful results.

You'll also use basic XAML to create a simple GUI, so you can test the image classifier. 

### Create the app

1. Open Visual Studio and choose `create a new project`.

![Create a new Visual Studio project](../../images/tutorials/mlnet/vs-new-project.png)

2. In the search bar, type `UWP` and then select `Blank APP (Universal Windows`). This opens a new C# project for a single-page Universal Windows Platform (UWP) app that has no predefined controls or layout. Select `Next` to open a configuration window for the project. 

![Create a UWP app](../../images/tutorials/mlnet/new-uwp-app.png)

3. In the configuration window:

*	Choose a name for your project. Here, we use **classifierMLNETModel**.
*	Choose the location of your project. 
*	If you're using VS 2019, ensure `Place solution and project in the same directory` is unchecked.
*	If you're using VS 2017, ensure `Create directory for solution` is checked.

![Configure your app](../../images/tutorials/mlnet/dotnet-configure.png)

Press `create` to create your project. The minimum target version window may pop up. Be sure your minimum version is set to **Windows 10 build 17763** or later.

To create an app and deploy a model with a WinML app, you'll need the following: 

4. After the project was created, navigate to the project folder, open the assets folder [….\classifierMLNETModel\Assets], and copy your `bestModel.onnx` file to this location. 

### Explore project solution

Let's explore your project solution.

Visual Studio automatically created several cs-code files inside the Solution Explorer. `MainPage.xaml` contains the XAML code for your GUI, and `MainPage.xaml.cs` contains your application code. If you've created a UWP app before, these files should be very familiar to you.

## Create the Application GUI 

First, let's create a simple GUI for your app.

1. Double-click on the `MainPage.xaml` file. In your blank app, the XAML template for your app's GUI is empty, so we'll need to add some UI features.

2. Replace the code of `MainPage.xaml` with the following.

```xaml
<Page
    x:Class="classifierMLNETModel.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:classifierMLNETModel"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

	
        <StackPanel Margin="1,0,-1,0">
            <TextBlock x:Name="Menu" 
                       FontWeight="Bold" 
                       TextWrapping="Wrap"
                       Margin="10,0,0,0"
                       Text="Image Classification"/>
            <TextBlock Name="space" />
            <Button Name="recognizeButton"
                    Content="Pick Image"
                    Click="OpenFileButton_Click" 
                    Width="110"
                    Height="40"
                    IsEnabled="True" 
                    HorizontalAlignment="Left"/>
            <TextBlock Name="space3" />
            <Button Name="Output"
                    Content="Result is:"
                    Width="110"
                    Height="40"
                    IsEnabled="True" 
                    HorizontalAlignment="Left" 
                    VerticalAlignment="Top">
            </Button>
            <!--Dispaly the Result-->
            <TextBlock Name="displayOutput" 
                       FontWeight="Bold" 
                       TextWrapping="Wrap"
                       Margin="25,0,0,0"
                       Text="" Width="1471" />
            <TextBlock Name="space2" />
            <!--Image preview -->
            <Image Name="UIPreviewImage" Stretch="Uniform" MaxWidth="300" MaxHeight="300"/>
        </StackPanel>
    </Grid> 
</Page>
```

## Add the model to the project using Windows Machine Learning Code Generator

Windows Machine Learning Code Generator, or **mlgen**, is a Visual Studio extension to help you get started using WinML APIs on UWP apps. It generates template code when you add a trained ONNX file into the UWP project. 

Windows Machine Learning's code generator mlgen creates an interface (for C#, C++/WinRT, and C++/CX) with wrapper classes that call the Windows ML API for you. This allows you to easily load, bind, and evaluate a model in your project. We'll use it in this tutorial to handle many of those functions for us.

Code generator is available for Visual Studio 2017 and later. Please be aware that in Windows 10, version 1903 and later, mlgen is no longer included in the Windows 10 SDK, so you must download and install the extension. If you've been following this tutorial from the introduction, you will have already handled this, but if not, you should download for [VS 2019](https://marketplace.visualstudio.com/items?itemName=WinML.mlgenv2) or for [VS 2017](https://marketplace.visualstudio.com/items?itemName=WinML.mlgen).

> [!NOTE]
> To learn more about mlgen, please see the [mlgen documentation](https://docs.microsoft.com/windows/ai/windows-ml/mlgen)

1. If you haven't already, install mlgen.

2. Right-click on the `Assets` folder in the Solution Explorer in Visual studio, and select `Add > Existing Item`. 

3. Navigate to the assets folder inside `ImageClassifierAppUWP [….\ImageClassifierAppUWP\Assets]`, find the ONNX model you previously copied there, and select `add`.

4. After you added an ONNX model (name: “classifier”) to the assets folder in solution explorer in VS, the project should now have two new files: 
*	`bestModel.onnx`  - this is your model in ONNX format.
*	`bestModel.cs` – automatically generated WinML code file. 

![Project structure with ONNX model added](../../images/tutorials/pytorch/app-with-classifier.png)

5. To make sure the model builds when you compile our application, select the `bestModel.onnx` file and choose `Properties`. For `Build Action`, select `Content`.

Now, let’s explore the newly generated code in the `bestModel.cs` file. 

The generated code includes three classes:

* `bestModelModel`: This class includes two methods for model instantiation and model evaluation. It will help us to create the machine learning model representation, create a session on the system default device, bind the specific inputs and outputs to the model, and evaluate the model asynchronously. 
* `bestModelInput`: This class initializes the input types that the model expects. The model input depends on the model requirements for input data.
* `bestModelOutput`: This class initializes the types that the model will output. The model output depends on how it is defined by the model.

You'll now use these classes to load, bind, and evaluate the model in our project.

### Tensor conversion

To make it easier dealing with tensorization, change the input `TensorFloat` class to `ImageFeatureValue`.

1. Make the following changes in the `bestModel.cs` file: 

The code:

```csharp
public sealed class bestModelInput
    {
        public TensorFloat input; // shape(-1,3,32,32)
    }
```
Will become:

```csharp
public sealed class bestModelInput
    {
        public ImageFeatureValue input; // shape(-1,3,32,32)
    }
```

## Load the model & inputs

### Load the model

1. Double-click on the `MainPage.xaml.cs` code file to open the application code.

2. Replace the “using” statements with the following, to get an access to all the APIs that you'll need.

```csharp
// Specify all the using statements which give us the access to all the APIs that you'll need
using System;
using System.Threading.Tasks;
using Windows.AI.MachineLearning;
using Windows.Graphics.Imaging;
using Windows.Media;
using Windows.Storage;
using Windows.Storage.Pickers;
using Windows.Storage.Streams;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media.Imaging;
```

3. Add the following variable declarations after the using statements inside your `MainPage` class, under the namespace `classifierMLNETModel`.

```csharp
		// All the required fields declaration
		private bestModelModel modelGen;
		private bestModelInput image = new bestModelInput();
		private bestModelOutput results;
		private StorageFile selectedStorageFile;
		private string label = "";
		private float probability = 0;
		private Helper helper = new Helper();

		public enum Labels
		{
			desert,
			soup,
			vegetable_fruit,
		}
```

Now, you'll implement the `LoadModel` method. The method will access the ONNX model and store it in memory. Then, you'll use the `CreateFromStreamAsync` method to instantiate the model as a `LearningModel` object. The `LearningModel` class represents a trained machine learning model. Once instantiated, the `LearningModel` is the initial object you use to interact with Windows ML. 

To load the model, you can use several static methods in the `LearningModel` class. In this case, you'll use the `CreateFromStreamAsync` method. 

The `CreateFromStreamAsync` method was automatically created with mlgen, so you don't need to implement this method. You can review this method by double clicking on the `bestModel.cs` file generated file by mlgen.

To learn more about `LearningModel` class, please review the [LearningModel Class documentation](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel?view=winrt-19041&preserve-view=true). 

To learn more about additional ways of loading the model, please review the [Load a model documentation](https://docs.microsoft.com/windows/ai/windows-ml/load-a-model)

4. Let’s define the main method.

```csharp
// The main page to initialize and execute the model.
public MainPage()
{
	this.InitializeComponent();
	loadModel();
}
```

5. Add the implementation of the `loadModel` method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
private async Task loadModel()
{
// Get an access the ONNX model and save it in memory. 
	StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/bestModel.onnx"));
// Instantiate the model. 
	modelGen = await bestModelModel.CreateFromStreamAsync(modelFile);
}
```

### Load the Image

1. We need to define a click event to initiate the sequence of four method calls for model execution – conversion, binding and evaluation, output extraction and display the results. Add the following method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
        // Waiting for a click event to select a file 
        private async void OpenFileButton_Click(object sender, RoutedEventArgs e)
        {
            if (!await getImage())
            {
                return;
            }
            // After the click event happened and an input selected, begin the model execution. 
            // Bind the model input
            await imageBind();
            // Model evaluation
            await evaluate();
            // Extract the results
            extractResult();
            // Display the results  
            await displayResult();
        }
```

2. Now, you'll implement the `getImage()` method. This method will select an input image file and save it in memory. Add the following method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
        // A method to select an input image file
        private async Task<bool> getImage()
        {
            try
            {
                // Trigger file picker to select an image file
                FileOpenPicker fileOpenPicker = new FileOpenPicker();
                fileOpenPicker.SuggestedStartLocation = PickerLocationId.PicturesLibrary;
                fileOpenPicker.FileTypeFilter.Add(".jpg");
                fileOpenPicker.FileTypeFilter.Add(".png");
                fileOpenPicker.ViewMode = PickerViewMode.Thumbnail;
                selectedStorageFile = await fileOpenPicker.PickSingleFileAsync();
                if (selectedStorageFile == null)
                {
                    return false;
                }
            }
            catch (Exception)
            {
                return false;
            }
            return true;
        }
```

Next, you will implement an image `Bind()` method to get the representation of the file in the bitmap BGRA8 format. But first, you'll create a helper class to resize the image.  

3. To create a helper file, right-click on the solution name (`ClassifierPyTorch`), then choose `Add a new item`. In the open window, select `Class` and give it a name. Here, we're calling it `Helper`.  

![Add a Helper file](../../images/tutorials/mlnet/helper-class-creation.png)

4. A new class file will appear within your project. Open this class, and add the following code:
 
```csharp
using System; 
using System.Threading.Tasks; 
using Windows.Graphics.Imaging; 
using Windows.Media; 

namespace classifierPyTorch 
{ 
    public class Helper 
    { 
        private const int SIZE = 32;  
        VideoFrame cropped_vf = null; 
 
        public async Task<VideoFrame> CropAndDisplayInputImageAsync(VideoFrame inputVideoFrame) 
        { 
            bool useDX = inputVideoFrame.SoftwareBitmap == null; 

            BitmapBounds cropBounds = new BitmapBounds(); 
            uint h = SIZE; 
            uint w = SIZE; 
            var frameHeight = useDX ? inputVideoFrame.Direct3DSurface.Description.Height : inputVideoFrame.SoftwareBitmap.PixelHeight; 
            var frameWidth = useDX ? inputVideoFrame.Direct3DSurface.Description.Width : inputVideoFrame.SoftwareBitmap.PixelWidth; 
 
            var requiredAR = ((float)SIZE / SIZE); 
            w = Math.Min((uint)(requiredAR * frameHeight), (uint)frameWidth); 
            h = Math.Min((uint)(frameWidth / requiredAR), (uint)frameHeight); 
            cropBounds.X = (uint)((frameWidth - w) / 2); 
            cropBounds.Y = 0; 
            cropBounds.Width = w; 
            cropBounds.Height = h; 
 
            cropped_vf = new VideoFrame(BitmapPixelFormat.Bgra8, SIZE, SIZE, BitmapAlphaMode.Ignore); 
 
            await inputVideoFrame.CopyToAsync(cropped_vf, cropBounds, null); 
            return cropped_vf; 
        } 
    } 
} 
```

Now, let's convert the image to the appropriate format.

The `bestModelInput` class initializes the input types that the model expects. In our case, we configured our code to expect an `ImageFeatureValue`.

The `ImageFeatureValue` class describes the properties of the image used to pass into a model. To create an`ImageFeatureValue`, you use `CreateFromVideoFrame` method. For more specific details of why this is the case and how these classes and methods work, see the [ImageFeatureValue class documentation](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturevalue?view=winrt-19041&preserve-view=true).

> [!NOTE]
> In this tutorial, we use the `ImageFeatureValue` class instead of a tensor. If Window ML does not support your model’s color format, this won't be an option. For an example of how to work with image conversions and tensorization, see the [Custom Tensorization Sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization).

5. Add the implementation of the `convert()` method to your `MainPage.xaml.cs` code file inside the MainPage class. The convert method will get us a representation of the input file in a BGRA8 format.

```csharp
// A method to convert and bind the input image.  
        private async Task imageBind()
        {
            UIPreviewImage.Source = null;
            try
            {
                SoftwareBitmap softwareBitmap;
                using (IRandomAccessStream stream = await selectedStorageFile.OpenAsync(FileAccessMode.Read))
                {
                    // Create the decoder from the stream 
                    BitmapDecoder decoder = await BitmapDecoder.CreateAsync(stream);
                    // Get the SoftwareBitmap representation of the file in BGRA8 format
                    softwareBitmap = await decoder.GetSoftwareBitmapAsync();
                    softwareBitmap = SoftwareBitmap.Convert(softwareBitmap, BitmapPixelFormat.Bgra8, BitmapAlphaMode.Premultiplied);
                }
                // Display the image
                SoftwareBitmapSource imageSource = new SoftwareBitmapSource();
                await imageSource.SetBitmapAsync(softwareBitmap);
                UIPreviewImage.Source = imageSource;

				// Encapsulate the image within a VideoFrame to be bound and evaluated
            	VideoFrame inputImage = VideoFrame.CreateWithSoftwareBitmap(softwareBitmap);
              	// Resize the image size to 224x224 
              	inputImage=await helper.CropAndDisplayInputImageAsync(inputImage);
              	// Bind the model input with image
              	ImageFeatureValue imageTensor = ImageFeatureValue.CreateFromVideoFrame(inputImage);
				image.input1 = imageTensor;

				// Encapsulate the image within a VideoFrame to be bound and evaluated
				VideoFrame inputImage = VideoFrame.CreateWithSoftwareBitmap(softwareBitmap);
				// Bind the input image
				ImageFeatureValue imageTensor = ImageFeatureValue.CreateFromVideoFrame(inputImage);
				image.modelInput = imageTensor;

            }
            catch (Exception e)
            {
            }
        }
```


## Bind and Evaluate the model

Next, you'll create a session based on the model, bind the input and output from the session, and evaluate the model.

### Create a session to bind the model:

To create a session, you use the `LearningModelSession` class. This class is used to evaluate machine learning models, and binds the model to a device that then runs and evaluates the model.  You can select a device when you create a session to execute your model on a specific device of your machine. The default device is the CPU.

> [!NOTE]
> To learn more about how to choose a device, please review the [Create a session](https://docs.microsoft.com/windows/ai/windows-ml/create-a-session) documentation.

### Bind model inputs and outputs:

To bind input and output, you use the `LearningModelBinding` class. A machine learning model has input and output features, which pass information into and out of the model. Be aware that required features must be supported by the Window ML APIs. The `LearningModelBinding` class is applied on a `LearningModelSession` to bind values to named input and output features.

The implementation of the binding is automatically generated by mlgen, so you don’t have to take care of it. The binding is implemented by calling the predefined methods of the `LearningModelBinding` class. In our case, it uses the `Bind` method to bind a value to the named feature type.

### Evaluate the model:

After you create a session to bind the model and bounded values to a model’s inputs and outputs, you can evaluate the model’s inputs and get its predictions. To run the model execution, you should call any of the predefined evaluate methods on the LearningModelSession. In our case, we'll use the `EvaluateAsync` method.
 
Similar to `CreateFromStreamAsync`, the `EvaluateAsync` method was also automatically generated by WinML Code Generator, so you don’t need to implement this method. You can review this method in the `bestModel.cs` file.

The `EvaluateAsync` method will asynchronously evaluate the machine learning model using the feature values already bound in bindings. It will create a session with `LearningModelSession`, bind the input and output with `LearningModelBinding`, execute the model evaluation, and get the output features of the model using the `LearningModelEvaluationResult` class.

> [!NOTE]
> To learn about other evaluation methods to run the model, please check which methods can be implemented on the LearningModelSession by reviewing the [LearningModelSession Class documentation](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsession?view=winrt-19041&preserve-view=true). 

1. Add the following method to your `MainPage.xaml.cs` code file inside the MainPage class to create a session, bind and evaluate the model.

```csharp
        // A method to evaluate the model
        private async Task evaluate()
        {
            results = await modelGen.EvaluateAsync(image);
        }
```

## Extract and display the results

You'll now need to extract the model output and display the right result, which you'll do by implementing the `extractResult` and `displayResult` methods. You'll need to find the highest probability to return the correct label. 

1. Add the `extractResult` method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
        // A method to extract output from the model 
        private void extractResult()
        {
            // Retrieve the results of evaluation
            var mResult = results.modelOutput as TensorFloat;
            // convert the result to vector format
            var resultVector = mResult.GetAsVectorView();
            
            probability = 0;
            int index = 0;
            // find the maximum probability
            for(int i=0; i<resultVector.Count; i++)
            {
                var elementProbability=resultVector[i];
                if (elementProbability > probability)
                {
                    index = i;
                }
            }
            label = ((Labels)index).ToString();
        }
```

2. Add the `displayResult` method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
        private async Task displayResult() 
        {
            displayOutput.Text = label; 
        }
``` 

That’s it! You've successfully created the Windows machine learning app with a basic GUI to test our classification model. The next step is to launch the application and run it locally on your Windows device.  

## Launch the application 

Once you completed the application interface, added the model, and generated the Windows ML code, you can test the application!  

Enable developer mode and test your application from Visual Studio. Make sure the dropdown menus in the top toolbar are set to `Debug`. Change the Solution Platform to x64 to run the project on your local machine if your device is 64-bit, or x86 if it's 32-bit. 

To test our app, let's use the below image of soup. Let’s see how our app classifies the content of the image. 

![Image for application testing](../../images/tutorials/mlnet/soup-image.png)

1. Save this image on your local device to test the app. Change the image format to `.jpg` if required. You can also add any other relevant image from your local device in a `.jpg` or `.png` format. 

2. To run the project, select the `Start Debugging` button on the toolbar, or press `F5`. 

3. When the application starts, press `Pick Image` and select the image from your local device.  

![Application interface](../../images/tutorials/mlnet/mlnet-app.png)

The result will appear on the screen right away. As you can see, our Windows ML app successfully classified the image as a soup.

![Successful classification in your app](../../images/tutorials/mlnet/mlnet-successful-classification.png)

### Summary

You've just made your first Windows Machine Learning app, from model creation to successful execution.

### Additional Resources

To learn more about topics mentioned in this tutorial, visit the following resources:
*   Windows ML tools: Learn more tools like the [Windows ML Dashboard](../dashboard.md), [WinMLRunner](../winmlrunner.md), and the [mglen](../mlgen.md) Windows ML code generator. 
*   [ONNX model](https://docs.microsoft.com/windows/ai/windows-ml/get-onnx-model): Learn more about the ONNX format.
*   [Windows ML performance and memory](https://docs.microsoft.com/windows/ai/windows-ml/performance-memory): Learn more how to manage app performance with Windows ML. 
*   [Windows Machine Learning API reference](https://docs.microsoft.com/windows/ai/windows-ml/api-reference): Learn more about three areas of Windows ML APIs.