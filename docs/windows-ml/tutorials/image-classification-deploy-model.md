---
title: Deploy your model in a Windows app with Windows ML API
description: Deploy your image classification model in a Windows App
ms.date: 2/24/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials
ms.localizationpriority: medium
---

# Deploy your model in a Windows app with the Windows Machine Learning APIs

[In the previous part of this tutorial](image-classification-train-model.md), you learned how to build and export a model in ONNX format. Now that you have that model, you can embed it into a Windows application and run it locally on a device by calling WinML APIs. 

Once we're done, you'll have a working Image classifier WinML UWP app (C#). 

## About the sample app

Using our model, we'll create an app that can classify images of food. It allows you to select an image from your local device and process it by a locally stored classification ONNX model you built and trained [in the previous part](image-classification-train-model.md). The tags returned are displayed next to the image, as well as the the confidence probability of the classification.

If you've been following this tutorial so far, you should already have the necessary prerequisites for app development in place. If you need a refresher, see [the first part of this tutorial](image-classification-intro.md).

> [!NOTE]
> If you prefer to download the complete sample code, you can [download the solution file here](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/Tutorial%20Samples/Custom%20Vision%20and%20Windows%20ML). Download a local copy of the repo, navigate to this sample, then open the `ImageClassifierAppUWP.sln` file with Visual Studio. Then, you can skip to the [Launch the application](#Launch the application) step.

## Create a WinML UWP (C#) 

Below, we'll show you how to create your app and WinML code from scratch. You'll learn how to:

*	Load a machine learning model.
*	Load an image in the required format.
*	Bind the model's inputs and outputs.
*	Evaluate the model and display meaningful results.

You'll also use basic XAML to create a simple GUI, so you can test the image classifier. 

### Create the app

1. Open Visual Studio and choose `create a new project`.

![Create a new Visual Studio project](../../images/tutorials/create-new-project.png)

2. In the search bar, type `UWP` and then select `Blank APP (Universal Windows`). This opens a new C# project for a single-page Universal Windows Platform (UWP) app that has no predefined controls or layout. Select `Next` to open a configuration window for the project. 

![Create a new UWP app](../../images/tutorials/new-uwp-app.png)

3. In the configuration window:

*	Choose a name for your project. Here, we use **ImageClassifierAppUWP**.
*	Choose the location of your project. 
*	If you're using VS 2019, ensure `Place solution and project in the same directory` is unchecked.
*	If you're using VS 2017, ensure `Create directory for solution` is checked.

Press `create` to create your project. The minimum target version window may pop up. Be sure your minimum version is set to **Windows 10 build 17763** or later.

To create an app and deploy a model with a WinML app, you'll need the following: 

4. After the project was created, navigate to the project folder, open the assets folder [….\ImageClassifierAppUWP\Assets], and copy your model to this location. 

5. Change the model name from `model.onnx` to `classifier.onnx`. This makes things a little clearer, and aligns it to the format of the tutorial.

### Explore your model

Let's get familiar with the structure of your Model file.

1. Open your `classifier.onnx` model file, using **Neutron.**

2. Press `Data` to open the model properties.

![Model properties](../../images/tutorials/model-properties.png)

As you can see, the model requires 32-bit Tensor (multidimensional array) float object as an input and returns two outputs: the first named `classLabel` is tensor of strings and the second named `loss` is a sequence of string-to-float maps that describe the probability for each labeled classification. You'll need this information to successfully display the model output in your Windows app.

### Explore project solution

Let's explore your project solution.

Visual Studio automatically created several cs-code files inside the Solution Explorer. `MainPage.xaml` contains the XAML code for your GUI, and `MainPage.xaml.cs` contains your application code. If you've created a UWP app before, these files should be very familiar to you.

## Create the Application GUI 

First, let's create a simple GUI for your app.

1. Double-click on the `MainPage.xaml` file. In your blank app, the XAML template for your app's GUI is empty, so we'll need to add some UI features.

2. Add the below code to the main body of `MainPage.xaml`.

```xaml
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
            <!--Display the Result-->
            <TextBlock Name="displayOutput" 
                       FontWeight="Bold" 
                       TextWrapping="Wrap"
                       Margin="30,0,0,0"
                       Text="" Width="1471" />
            <Button Name="ProbabilityResult"
                    Content="Probability is:"
                    Width="110"
                    Height="40"
                    IsEnabled="True" 
                    HorizontalAlignment="Left"/>
            <!--Display the Result-->
            <TextBlock Name="displayProbability" 
                       FontWeight="Bold" 
                       TextWrapping="Wrap"
                       Margin="30,0,0,0"
                       Text="" Width="1471" />
            <TextBlock Name="space2" />
            <!--Image preview -->
            <Image Name="UIPreviewImage" Stretch="Uniform" MaxWidth="300" MaxHeight="300"/>
        </StackPanel>
    </Grid>
```

## Windows Machine Learning Code Generator

Windows Machine Learning Code Generator, or **mlgen**, is a Visual Studio extension to help you get started using WinML APIs on UWP apps. It generates template code when you add a trained ONNX file into the UWP project. 

Windows Machine Learning's code generator mlgen creates an interface (for C#, C++/WinRT, and C++/CX) with wrapper classes that call the Windows ML API for you. This allows you to easily load, bind, and evaluate a model in your project. We'll use it in this tutorial to handle many of those functions for us.

Code generator is available for Visual Studio 2017 and later. Please be aware that in Windows 10, version 1903 and later, mlgen is no longer included in the Windows 10 SDK, so you must download and install the extension. If you've been following this tutorial from the introduction, you will have already handled this, but if not, you should download for [VS 2019](https://marketplace.visualstudio.com/items?itemName=WinML.mlgenv2) or for [VS 2017](https://marketplace.visualstudio.com/items?itemName=WinML.mlgen).

> [!NOTE]
> To learn more about mlgen, please see the [mlgen documentation](https://docs.microsoft.com/windows/ai/windows-ml/mlgen)

1. If you haven't already, install mlgen.

2. Right-click on the `Assets` folder in the Solution Explorer in Visual studio, and select `Add > Existing Item`. 

3. Navigate to the assets folder inside `ImageClassifierAppUWP [….\ImageClassifierAppUWP\Assets]`, find the ONNX model you previously copied there, and select `add`.

4. After you added an ONNX model (name: “classifier”) to the assets folder in solution explorer in VS, the project should now have two new files: 
*	`classifier.onnx`  - this is your model in ONNX format.
*	`classifier.cs` – automatically generated WinML code file. 

![Project structure with ONNX model added](../../images/tutorials/app-with-classifier.png)

5. To make sure the model builds when you compile our application, select the `classifier.onnx` file and choose `Properties`. For `Build Action`, select `Content`.

Now, let’s explore the newly generated code in the classifier.cs file. 

The generated code includes three classes:

* `classifierModel`: This class includes two methods for model instantiation and model evaluation. It will help us to create the machine learning model representation, create a session on the system default device, bind the specific inputs and outputs to the model, and evaluate the model asynchronously. 
* `classifierInput`: This class initializes the input types that the model expects. The model input depends on the model requirements for input data. In our case, the input expects an ImageFeatureValue, a class which describes the properties of the image used to pass into a model.
* `classifierOutput`: This class initializes the types that the model will output. The model output depends on how it is defined by the model. In our case, the output will be a sequence of map (dictionaries) of type String and TensorFloat (Float32) called loss.

You'll now use these classes to load, bind, and evaluate the model in our project.

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

3. Add the following variable declarations after the using statements inside your `MainPage` class, under the namespace `ImageClassifierAppUWP`.

```csharp
        // All the required variable declaration
        private classifierModel modelGen;
        private classifierInput input = new classifierModelInput();
        private classifierOutput output;
        private StorageFile selectedStorageFile;
        private string result = "";
        private float resultProbability = 0;
```

The result will look as follows.

```csharp
// Specify all the using statements which give us the access to all the APIs that we'll need
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

namespace ImageClassifierAppUWP
{
    public sealed partial class MainPage : Page
    {
        // All the required fields declaration
        private classifierModel modelGen;
        private classifierInput input = new classifierInput();
        private classifierOutput output;
        private StorageFile selectedStorageFile;
        private string result = "";
        private float resultProbability = 0;
```

Now, you'll implement the `LoadModel` method. The method will access the ONNX model and store it in memory. Then, you'll use the `CreateFromStreamAsync` method to instantiate the model as a `LearningModel` object. The `LearningModel` class represents a trained machine learning model. Once instantiated, the `LearningModel` is the initial object you use to interact with Windows ML. 

To load the model, you can use several static methods in the `LearningModel` class. In this case, you'll use the `CreateFromStreamAsync` method. 

The `CreateFromStreamAsync` method was automatically created with mlgen, so you don't need to implement this method. You can review this method by double clicking on the `classifier.cs` file generated file by mlgen.

To learn more about `LearningModel` class, please review the [LearningModel Class documentation](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel?view=winrt-19041&preserve-view=true). 
To learn more about additional ways of loading the model, please review the [Load a model documentation](https://docs.microsoft.com/windows/ai/windows-ml/load-a-model)

4. Add a `loadModel` method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
        private async Task loadModel()
        {
            // Get an access the ONNX model and save it in memory.
            StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/classifier.onnx"));
            // Instantiate the model. 
            modelGen = await classifierModel.CreateFromStreamAsync(modelFile);
        }
```

5. Now, add a call to the new method to the constructor of the class.

```csharp
        // The main page to initialize and execute the model.
        public MainPage()
        {
            this.InitializeComponent();
            loadModel();
        }
```

The result will look as follows.

```csharp
        // The main page to initialize and execute the model.
        public MainPage()
        {
            this.InitializeComponent();
            loadModel();
        }

        // A method to load a machine learning model.
        private async Task loadModel()
        {
            // Get an access the ONNX model and save it in memory.  
            StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/classifier.onnx"));
            // Instantiate the model. 
            modelGen = await classifierModel.CreateFromStreamAsync(modelFile);
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

Now, you will implement an image `Bind()` method to get the representation of the file in the bitmap BGRA8 format.

3. Add the implementation of the `convert()` method to your `MainPage.xaml.cs` code file inside the MainPage class. The convert method will get us a representation of the input file in a BGRA8 format.

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
                // bind the input image
                ImageFeatureValue imageTensor = ImageFeatureValue.CreateFromVideoFrame(inputImage);
                input.data = imageTensor;
            }
            catch (Exception e)
            {
            }
        }
```

The result of the work done in this section will look as follows.


```csharp
        // Waiting for a click event to select a file 
        private async void OpenFileButton_Click(object sender, RoutedEventArgs e)
        {
            if (!await getImage())
            {
                return;
            }
            // After the click event happened and an input selected, we begin the model execution. 
            // Bind the model input
            await imageBind();
            // Model evaluation
            await evaluate();
            // Extract the results
            extractResult();
            // Display the results  
            await displayResult();
        }

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

                // bind the input image
                ImageFeatureValue imageTensor = ImageFeatureValue.CreateFromVideoFrame(inputImage);
                input.data = imageTensor;
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

Currently, Windows ML supports all ONNX feature types like Tensors (multi-dimensional arrays), Sequences (vectors of values), Map (value pairs of information) and Images (specific formats). All images will be represented in Windows ML in a tensor format. Tensorization is the process of converting an image into a tensor and happens during bind. 

Fortunately, you don’t have to take care of tensorization conversion. The `ImageFeatureValue` method you used in the previous part takes care of both conversion and tensorization, so the images match the model's required image format.

> [!NOTE]
> To learn more about how to bind a `LearningModel` and about types of features supported by WinML, please review the [Bind a model](https://docs.microsoft.com/windows/ai/windows-ml/bind-a-model) documentation.

### Evaluate the model:

After you create a session to bind the model and bounded values to a model’s inputs and outputs, you can evaluate the model’s inputs and get its predictions. To run the model execution, you should call any of the predefined evaluate methods on the LearningModelSession. In our case, we'll use the `EvaluateAsync` method.
 
Similar to `CreateFromStreamAsync`, the `EvaluateAsync` method was also automatically generated by WinML Code Generator, so you don’t need to implement this method. You can review this method in the `classifier.cs` file.

The `EvaluateAsync` method will asynchronously evaluate the machine learning model using the feature values already bound in bindings. It will create a session with `LearningModelSession`, bind the input and output with `LearningModelBinding`, execute the model evaluation, and get the output features of the model using the `LearningModelEvaluationResult` class.

> [!NOTE]
> To learn about other evaluation methods to run the model, please check which methods can be implemented on the LearningModelSession by reviewing the [LearningModelSession Class documentation](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsession?view=winrt-19041&preserve-view=true). 

1. Add the following method to your `MainPage.xaml.cs` code file inside the MainPage class to create a session, bind and evaluate the model.

```csharp
        // A method to evaluate the model
        private async Task evaluate()
        {
            output = await modelGen.EvaluateAsync(input);
        }
```

## Extract and display the results

You will now need to extract the model output and display the right results. You will do it by implementing the `extractResult` and `displayResult` methods.

As you explored earlier, the model returns two outputs: the first named `classLabel` is tensor of strings and the second named loss is a sequence of string-to-float maps that describes the probability for each labeled classification. So, to successfully display the result and the probability, all we need is to extract the output from the loss output. We'll need to find the highest probability to return the correct result.

1. Add the `extractResult` method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
        private void extractResult()
        {
        // A method to extract output (result and a probability) from the "loss" output of the model 
            var collection = output.loss;
            float maxProbability = 0;
            string keyOfMax = "";

            foreach (var dictionary in collection)
            {
                foreach (var key in dictionary.Keys)
                {
                    if (dictionary[key] > maxProbability)
                    {
                        maxProbability = dictionary[key];
                        keyOfMax = key;
                    }
                }
            }
            result = keyOfMax;
            resultProbability = maxProbability;
        }
```

2. Add the `displayResult` method to your `MainPage.xaml.cs` code file inside the `MainPage` class.

```csharp
        // A method to display the results
        private async Task displayResult()
        {
            displayOutput.Text = result.ToString();
            displayProbability.Text = resultProbability.ToString();
        }
```

The result of the **Bind and Evaluate** and the **Extract and display the results** parts of the WinML code of our app will look as following.


```csharp
        // A method to evaluate the model
        private async Task evaluate()
        {
            output = await modelGen.EvaluateAsync(input);
        }

        // A method to extract output (string and a probability) from the "loss" output of the model 
        private void extractResult()
        {
            var collection = output.loss;
            float maxProbability = 0;
            string keyOfMax = "";

            foreach (var dictionary in collection)
            {
                foreach (var key in dictionary.Keys)
                {
                    if (dictionary[key] > maxProbability)
                    {
                        maxProbability = dictionary[key];
                        keyOfMax = key;
                    }
                }
            }
            result = keyOfMax;
            resultProbability = maxProbability;
        }

        // A method to display the results
        private async Task displayResult()
        {
            displayOutput.Text = result.ToString();
            displayProbability.Text = resultProbability.ToString();
        }
```

That’s it! You have successfully created the Windows machine learning app with a basic GUI to test our classification model. The next step is to launch the application and run it locally on your Windows device. 

## Launch the application

Once you completed the application interface, added the model, and generated the WinML code, you can test the application. Ensure the dropdown menus in the top toolbar are set to `Debug`. Change the `Solution Platform` to `x64` to run the project on your local machine if your device is 64-bit, or `x86` if it's 32-bit.

To test our app, you will use the below image of fruits. Let’s see how our app classifies the content of the image. 

![Example fruit image](../../images/tutorials/example-fruit.png)

1. Save this image on your local device to test the app. Change the image format to jpg if required. You can also add any other relevant image from your local device in a suitable format - .jpg, .png, .bmp, or .gif formats.

2. To run the project, press the `Start Debugging` button on the toolbar, or press `F5`.

3. When the application starts, press `Pick Image` and select the image from your local device.

![Pick image dialog](../../images/tutorials/pick-image.png)

The result will appear on the screen right away. As you can see, our WinML app successfully classified the image as fruits or vegetables, with a 99.9% confidence rating.

![Successful image classification](../../images/tutorials/classification-success.png)

### Summary

You've just made your first Windows Machine Learning app, from model creation to successful execution.

### Additional Resources

To learn more about topics mentioned in this tutorial, visit the following resources:
*   Windows ML tools: Learn more tools like the [Windows ML Dashboard](../dashboard.md), [WinMLRunner](../winmlrunner.md), and the [mglen](../mlgen.md) Windows ML code generator. 
*   [ONNX model](https://docs.microsoft.com/windows/ai/windows-ml/get-onnx-model): Learn more about the ONNX format.
*   [Windows ML performance and memory](https://docs.microsoft.com/windows/ai/windows-ml/performance-memory): Learn more how to manage app performance with Windows ML. 
*   [Windows Machine Learning API reference](https://docs.microsoft.com/windows/ai/windows-ml/api-reference): Learn more about three areas of Windows ML APIs.