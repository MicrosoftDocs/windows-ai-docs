---
author: eliotcowley
title: Create a Windows Machine Learning UWP application (C#)
description: Create your first UWP application with Windows ML in this step-by-step tutorial.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, windows machine learning, winml, windows ML
ms.localizationpriority: medium
---

# Tutorial: Create a Windows Machine Learning UWP application (C#)

In this tutorial, we'll build a simple Universal Windows Platform application that uses a trained machine learning model to recognize a numeric digit drawn by the user. This tutorial primarily focuses on how to load and use Windows ML in your UWP application.

## Prerequisites

- [Windows 10 Insider Preview](https://insider.windows.com/) (Version 1809 or higher)
- [Windows 10 SDK](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK) (Build 17763 or higher)
- [Visual Studio 2017](https://developer.microsoft.com/windows/downloads)

## 1. Download the sample

First, you'll need to download our [MNIST Tutorial](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/MNIST/Tutorial/cs) from GitHub. We've provided a template with implemented XAML controls and events, including:

- An [InkCanvas](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) to draw the digit.
- [Buttons](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.button) to interpret the digit and clear the canvas.
- Helper routines to convert the **InkCanvas** output to a [VideoFrame](https://docs.microsoft.com/uwp/api/windows.media.videoframe).

A [completed MNIST sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/MNIST/UWP/cs) is also available to download from GitHub.

## 2. Open the project in Visual Studio

Launch Visual Studio, and open the MNIST sample application. (If the solution is shown as unavailable, you'll need to right-click the project in the **Solution Explorer** and select **Reload Project**.)

Inside the **Solution Explorer**, the project has three main code files:

- `MainPage.xaml` - All of our XAML code to create the UI for the **InkCanvas**, buttons, and labels.
- `MainPage.xaml.cs` - Where our application code lives.
- `Helper.cs` - Helper routines to crop and convert image formats.

![Visual Studio solution explorer with project files](images/get-started1.png)

## 3. Build and run the project

In the Visual Studio toolbar, change the **Solution Platform** to **x64** to run the project on your local machine.

To run the project, click the **Start Debugging** button on the toolbar, or press **F5**. The application should show an **InkCanvas** where users can write a digit, a **Recognize** button to interpret the number, an empty label field where the interpreted digit will be displayed as text, and a **Clear Digit** button to clear the **InkCanvas**.

![Application screenshot](images/get-started2.png)

> [!NOTE]
> If the project won't build, you might need to change the project's deployment target version. Right-click the project in the **Solution Explorer** and select **Properties**. In the **Application** tab, set the **Target version** and **Min version** to match your OS and SDK.

## 4. Download a model

Next, let's get a machine learning model to add to our application. For this tutorial, we'll use a pre-trained **MNIST model** that was trained with the [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) and [exported to ONNX format](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb).

If you are using the MNIST Tutorial sample from GitHub, the MNIST model has already been included in your **Assets** folder, and you will need to add it to your application as an existing item. You can also download the pre-trained model from the [ONNX Model Zoo](https://github.com/onnx/models) on GitHub.

## 5. Add the model

After downloading the MNIST model, right click on the **Assets** folder in the **Solution Explorer**, and select **Add** > **Existing Item**. Point the file picker to the location of your ONNX model, and click **Add**.

The project should now have two new files:

- `mnist.onnx` - your trained model.
- `mnist.cs` - the Windows ML-generated code.

![Solution explorer with new files](images/get-started3.png)

To make sure the model builds when we compile our application, right click on the `mnist.onnx` file, and select **Properties**. For **Build Action**, select **Content**.

Now, let's take a look at the newly generated code in the `mnist.cs` file. We have three classes:

- **mnistModel** creates the machine learning model representation, creates a sessions on the system default device, binds the specific inputs and outputs to the model, and evaluates the model asynchronously. 
- **mnistInput** initializes the input types that the model expects. In this case, the input expects an [ImageFeatureValue](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturevalue).
- **mnistOutput** initializes the types that the model will output. In this case, the output will be a list called `Plus214_Output_0` of type [TensorFloat](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat).

We'll now use these classes to load, bind, and evaluate the model in our project.

## 6. Load, bind, and evaluate the model

For Windows ML applications, the pattern we want to follow is: Load > Bind > Evaluate.

- Load the machine learning model.
- Bind inputs and outputs to the model.
- Evaluate the model and view results.

We'll use the interface code generated in `mnist.cs` to load, bind, and evaluate the model in our application.

First, in `MainPage.xaml.cs`, let's instantiate the model, inputs, and outputs.

```csharp
namespace MNIST_Demo
{
	public sealed partial class MainPage : Page
	{
	    private mnistModel ModelGen;
	    private mnistInput ModelInput = new mnistInput();
	    private mnistOutput ModelOutput;
	    ...
	}
}
```

Then, in `LoadModelAsync`, we'll load the model. This method should be called before we use any of the models methods (i.e. on the Page's Loaded event, at a OnNavigatedTo override, or anywhere before `recognizeButton_Click` is called). The `mnistModel` class represents the MNIST model and creates the session on the system default device. To load the model, we call the `CreateFromStreamAsync` method, passing in the ONNX file as the parameter.

```csharp
private async Task LoadModelAsync()
{
    //Load a machine learning model
    StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/mnist.onnx"));
    ModelGen = await mnistModel.CreateFromStreamAsync(modelFile as IRandomAccessStreamReference);
}
```

Next, we want to bind our inputs and outputs to the model. The generated code also includes `mnistInput` and `mnistOutput` wrapper classes. The `mnistInput` class represents the model's expected inputs, and the `mnistOutput` class represents the model's expected outputs.

To initialize the model's input object, call the `mnistInput` class constructor, passing in your application data, and make sure that your input data matches the input type that your model expects. The `mnistInput` class expects an **ImageFeatureValue**, so we use a helper method to get an **ImageFeatureValue** for the input.

Using our included helper functions in `helper.cs`, we will copy the contents of the **InkCanvas**, convert it to type **ImageFeatureValue**, and bind it to our model.

```csharp
private async void recognizeButton_Click(object sender, RoutedEventArgs e)
{
    //Bind model input with contents from InkCanvas
    VideoFrame vf = await helper.GetHandWrittenImage(inkGrid);
    ModelInput.Input3 = ImageFeatureValue.CreateFromVideoFrame(vf);
}
```

For output, we simply call `EvaluateAsync` with the specified input. Once your inputs are initialized, call the model's `EvaluateAsync` method to evaluate your model on the input data. `EvaluateAsync` binds your inputs and outputs to the model object and evaluates the model on the inputs.

Since the model returns an output tensor, we'll first want to convert it to a friendly datatype, and then parse the returned list to determine which digit had the highest probability and display that one.

```csharp
private async void recognizeButton_Click(object sender, RoutedEventArgs e)
{
    //Bind model input with contents from InkCanvas
    VideoFrame vf = await helper.GetHandWrittenImage(inkGrid);
    ModelInput.Input3 = ImageFeatureValue.CreateFromVideoFrame(vf);

    //Evaluate the model
    ModelOutput = await ModelGen.EvaluateAsync(ModelInput);

    //Convert output to datatype
    IReadOnlyList<float> vectorImage = ModelOutput.Plus214_Output_0.GetAsVectorView();
    IList<float> imageList = vectorImage.ToList();

    //Query to check for highest probability digit
    var maxIndex = imageList.IndexOf(imageList.Max());

    //Display the results
    numberLabel.Text = maxIndex.ToString();
}
```

Finally, we'll want to clear out the **InkCanvas** to allow users to draw another number.

```csharp
private void clearButton_Click(object sender, RoutedEventArgs e)
{
    inkCanvas.InkPresenter.StrokeContainer.Clear();
    numberLabel.Text = "";
}
```

## 7. Launch the application

Once we build and launch the application, we'll be able to recognize a number drawn on the **InkCanvas**.

![complete application](images/get-started4.png)

That's it - you've made your first Windows ML application! For more samples that demonstrate how to use Windows ML, check out our [Windows-Machine-Learning](https://github.com/Microsoft/Windows-Machine-Learning) repo on GitHub.

[!INCLUDE [help](includes/get-help.md)]