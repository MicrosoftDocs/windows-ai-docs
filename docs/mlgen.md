---
author: serenaz
title: Add a model with mlgen
description: Add an ONNX model to your app with Windows ML's mlgen.
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, windows machine learning
ms.localizationpriority: medium
---

# Add a model with mlgen

Windows ML's code generator `mlgen` creates an interface (C# or C++/WinRT) with wrapper classes that call the [Windows ML API](/uwp/api/windows.ai.machinelearning.preview) for you, allowing you to easily load, bind, and evaluate the model in your project.

## Video summary

> [!VIDEO https://www.youtube.com/embed/8MCDSlm326U]

## mlgen

For UWP developers, `mlgen` is natively integrated with [Visual Studio (version 15.7)](https://developer.microsoft.com/windows/downloads). Inside your Visual Studio project, simply add your ONNX file to your project’s Assets, and VS will generate Windows ML wrapper classes in a new interface file.

For other workflows, or older versions of VS, you can use the command line tool `mlgen.exe`, which comes with the Windows SDK, to generate Windows ML wrapper classes. The tool is located in `(SDK_root)\bin\<version>\x64` or `(SDK_root)\bin\<version>\x86`, where SDK_root is the SDK installation directory. To run the tool, use the command below.

```
mlgen -i INPUT-FILE -l LANGUAGE -n NAMESPACE [-o OUTPUT-FILE]
```

Input parameters definition:

- `INPUT-FILE`: the ONNX model file
- `LANGUAGE`: CPP or CS
- `NAMESPACE`: the namespace of the generated code
- `OUTPUT-FILE`: file path where the generated code will be written to. If OUTPUT-FILE is not specified, the generated code is written to the standard output

The tool will output a file containing the interface classes, which you can then add to your VS project.

**Note**: To make sure your model builds when you compile your application, right click on the `.onnx` file, and select **Properties**. For **Build Action**, select **Content**.

Using the interface's generated wrapper classes, you'll follow the load, bind, and evaluate pattern to integrate your ML model into your app.

In the rest of the article, we'll use the mlgen interface from the [Get Started](get-started.md) MNST model to demonstrate how to load, bind, and evaluate a model in your app.

## Load

Once you have the generated wrapper classes, you'll load the model in your app.

The MNISTModel class represents the MNIST model, and to load the model, we call the CreateMNISTModel method, passing in the ONNX file as the parameter.

```csharp
// Load the model
StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/MNIST.onnx"));
MNISTModel model = MNISTModel.CreateMNISTModel(modelFile);
```

## Bind

The generated code also includes Input and Output wrapper classes. The Input class represents the model's expected inputs, and the Output class represents the model's expected outputs.

To initialize the model's input object, call the Input class constructor, passing in your application data, and make sure that your input data matches the input type that your model expects. The MNISTModelInput class expects a VideoFrame, so we use a helper method to get a VideoFrame for the input.

```csharp
//Bind the input data to the model
MNISTModelInputs ModelInput = new MNISTModelInputs();
ModelInput.Input3 = await helper.GetHandWrittenImage(inkGrid);
```

Output objects are initialized to *Null* values and will be updated with the model's results after the next step, Evaluate.

## Evaluate

Once your inputs are initialized, call the model's EvaluateAsync method to evaluate your model on the input data. EvaluateAsync binds your inputs and outputs to the model object and evaluates the model on the inputs.

```csharp
// Evaluate the model
MNISTModelOuput ModelOutput = model.EvaluateAsync(ModelInput);
```

After evaluation, your output contains the model's results, which you now can view and analyze. Since the MNIST model outputs a list of probabilities, we iterate through the list to find and display the digit with the highest probability.

```csharp
 //Iterate through output to determine highest probability digit
float maxProb = 0;
int maxIndex = 0;
for (int i = 0; i < 10; i++)
{
    if (ModelOutput.Plus214_Output_0[i] > maxProb)
    {
        maxIndex = i;
        maxProb = ModelOutput.Plus214_Output_0[i];
    }
}
numberLabel.Text = maxIndex.ToString();
```

## See also

- [Use the Windows ML APIs](winml-api.md)