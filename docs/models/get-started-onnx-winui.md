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

This article walks you through creating a WinUI 3 app that uses an ONNX model to evaluate a set of input sentences and determine which one most closely matches a target sentence.


## What is the ONNX runtime

[TBD - this summary is just the intro paragraph to the ONNX main page. Do we have anything else interesting to say here?]

ONNX Runtime is a cross-platform machine-learning model accelerator, with a flexible interface to integrate hardware-specific libraries. ONNX Runtime can be used with models from PyTorch, Tensorflow/Keras, TFLite, scikit-learn, and other frameworks. For more information, see the ONNX Runtime website at [https://onnxruntime.ai/docs/](https://onnxruntime.ai/docs/).


## Prerequisites

- Your device must have developer mode enabled. For more information see [Enable your device for development](/windows/apps/get-started/enable-your-device-for-development).
- Visual Studio 2022 or later with the [TBD - required workloads / components] workload.

## Create a new C# WinUI app

In Visual Studio, create a new project. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "winui", then select the **Black app, Packaged (WinUI3 in Desktop)** template. Name the new project "TBD_DescriptiveSampleName".

## Add references to Nuget packages

In **Solution Explorer**, right-click **Dependencies** and select **Manage NuGet packages...**. In the NuGet package manager, select the **Browse** tab. Search for the following package and for each, select the latest stable version in the **Version** drop-down and then click **Install**.

[TBD - Listing the packages from SentenceTransformerPlayground sample. Not sure if all of these are needed]

| Package | Description |
|---------|-------------|
| BERTTokenizers | TBD |
| libtorch-pcu-win-x64 TBD |
| Microsoft.ML.OnnxRuntime.DirectML | TBD |
| Microsoft.WindowsAppSDK | TBD |
| SharpDX.DXGI | TBD |
|TorchSharp |
| Vortice.DXGI |

## Add a model and vocabulary file to your project

In **Solution Explorer**, right-click your project and select **Add->New Folder**. Name the new folder "model". For this example, we will be using the model from [https://huggingface.co/optimum/all-MiniLM-L6-v2](https://huggingface.co/optimum/all-MiniLM-L6-v2). Go to the repo view for the model at [https://huggingface.co/optimum/all-MiniLM-L6-v2/tree/main](https://huggingface.co/optimum/all-MiniLM-L6-v2/tree/main). Click the **Download File* link for the files "model.onnx" and "vocab.txt". Copy these files into the "model" directory you just created.

[TBD - this is how I got the files to run the sample, but I'm not sure these are the best practices]

## Add code to use the model

The following sections all describe modifications you will make to the `MainWindow.xaml.cs` file in your project.

### Declare helper objects

Declare an class that derives from the **UncasedTokenizer** class provided by the BertTokenizers Nuget package, overriding the constructor to create an instance from the vocab file we added in a previous step. Place the following code inside the [TBD - SampleName] namespace, but outside of the **MainWindow** class definition.

```csharp
public class MyTokenizer : UncasedTokenizer
{
    public MyTokenizer(string vocabPath) : base(vocabPath){ }
}
```

Add a helper class that will store the input IDs, attention mask, and type IDs that we will pass into the model. Place the following code inside the [TBD - SampleName] namespace, but outside of the **MainWindow** class definition.

```csharp
public class ModelInput
{
    public long[] InputIds { get; set; }

    public long[] AttentionMask { get; set; }

    public long[] TokenTypeIds { get; set; }
}
```

Declare a class with an array will store the tensor data. Place the following code inside the [TBD - SampleName] namespace, but outside of the **MainWindow** class definition.

```csharp
public struct Vector
{
    public float[] data;
}
```



### Initialize the model

Inside the **MainWindow** class, create a helper method called **InitModel** that will initialize the model. [TBD - Note that I am being terse with descriptions of the code until I have confirmed that the code is approved / finalized.]

```csharp

private InferenceSession _inferenceSession;
private string modelDir = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "model");

private void InitModel()
{
    if (_inferenceSession != null)
    {
        return;
    }

    var factory1 = new Factory1();
    int deviceId = 0;
    Adapter1 selectedAdapter = null;
    for (int i = 0; i < factory1.GetAdapterCount1(); i++)
    {
        Adapter1 adapter = factory1.GetAdapter1(i);
        Debug.WriteLine($"Adapter {i}:");
        Debug.WriteLine($"\tDescription: {adapter.Description1.Description}");
        Debug.WriteLine($"\tDedicatedVideoMemory: {(long)adapter.Description1.DedicatedVideoMemory / 1000000000}GB");
        if(selectedAdapter == null || (long)adapter.Description1.DedicatedVideoMemory > (long)selectedAdapter.Description1.DedicatedVideoMemory)
        {
            selectedAdapter = adapter;
            deviceId = i;
        }
    }

    var sessionOptions = new SessionOptions
    {
        LogSeverityLevel = OrtLoggingLevel.ORT_LOGGING_LEVEL_INFO
    };
    sessionOptions.AppendExecutionProvider_DML(deviceId);
    _inferenceSession = new InferenceSession($@"{modelDir}\model.onnx", sessionOptions);
}
```

### Get the embeddings for the input text

Inside the **MainWindow** class, create a helper method called **GetEmbeddings** to get the embeddings for the sentences passed into the method. The embeddings is a vector of floating point numbers, such that the distance between two embeddings in the vector space is correlated with semantic similarity between two inputs in the original format.

```csharp
private float[] GetEmbeddings(params string[] sentences)
{
    InitModel();
    var tokenizer = new MyTokenizer($@"{modelDir}\vocab.txt");
    var tokens = tokenizer.Tokenize(sentences);
    var encoded = tokenizer.Encode(tokens.Count(), sentences);

    var input = new ModelInput()
    {
        InputIds = encoded.Select(t => t.InputIds).ToArray(),
        AttentionMask = encoded.Select(t => t.AttentionMask).ToArray(),
        TokenTypeIds = encoded.Select(t => t.TokenTypeIds).ToArray(),
    };

    var runOptions = new RunOptions();
    
    
    // Create input tensors over the input data.
    using var inputIdsOrtValue = OrtValue.CreateTensorValueFromMemory(input.InputIds,
          new long[] { sentences.Length, input.InputIds.Length });

    using var attMaskOrtValue = OrtValue.CreateTensorValueFromMemory(input.AttentionMask,
          new long[] { sentences.Length, input.AttentionMask.Length });

    using var typeIdsOrtValue = OrtValue.CreateTensorValueFromMemory(input.TokenTypeIds,
          new long[] { sentences.Length, input.TokenTypeIds.Length });

    var inputs = new Dictionary<string, OrtValue>
    {
        { "input_ids", inputIdsOrtValue },
        { "attention_mask", attMaskOrtValue },
        { "token_type_ids", typeIdsOrtValue }
    };

    using var output = _inferenceSession.Run(runOptions, inputs, _inferenceSession.OutputNames);
    var data = output.ToList()[0].GetTensorDataAsSpan<float>().ToArray();


    var sentence_embeddings = MeanPooling(data, input.AttentionMask, sentences.Length, input.AttentionMask.Length, 384);
    var denom = sentence_embeddings.norm(1, true, 2).clamp_min(1e-12).expand_as(sentence_embeddings);
    var results = sentence_embeddings / denom;
    return results.data<float>().ToArray();
}
```

### Implement average pooling for the embeddings

Inside the **MainWindow** class, create a helper method called **MeanPooling** that calculates the average of the embeddings to return a single tensor representation.

```c#
public static torch.Tensor MeanPooling(float[] embeddings, long[] attentionMask, long batchSize, long sequence, int hiddenSize)
{
    var tokenEmbeddings = torch.tensor(embeddings, new[] { batchSize, sequence, hiddenSize });
    var attentionMaskExpanded = torch.tensor(attentionMask, new[] { batchSize, sequence }).unsqueeze(-1).expand(tokenEmbeddings.shape).@float();

    var sumEmbeddings = (tokenEmbeddings * attentionMaskExpanded).sum(new[] { 1L });
    var sumMask = attentionMaskExpanded.sum(new[] { 1L }).clamp(1e-9, float.MaxValue);

    return sumEmbeddings / sumMask;
}
```

### Calculate rankings

Inside the **MainWindow** class, create a helper method called **CalculateRanking** that calculates the distance from each of the input sentences to the target sentence.

```csharp

public int[] CalculateRanking(Vector searchVector, Vector[] vectors)
{
    float[] scores = new float[vectors.Length];
    int[] indexranks = new int[vectors.Length];

    for (int i = 0; i < vectors.Length; i++)
    {
        var score = CosineSimilarity(vectors[i].data, searchVector.data);
        scores[i] = score;
    }

    var indexedFloats = scores.Select((value, index) => new { Value = value, Index = index })
      .ToArray();

    // Sort the indexed floats by value in descending order
    Array.Sort(indexedFloats, (a, b) => b.Value.CompareTo(a.Value));

    // Extract the top k indices
    indexranks = indexedFloats.Select(item => item.Index).ToArray();

    return indexranks;
}
```

### Call the model to find the closest match

In your app's button click handler, get the embeddings for the sentences to be ranked and the sentence to match. Then call **CalculateRanking** to determine which input sentence matches most closely.

```csharp
private void myButton_Click(object sender, RoutedEventArgs e)
{

    //_content = text.Split('.', '\r', '\n');
    //_content = _content.Where(x => !string.IsNullOrWhiteSpace(x)).ToArray();

    _content =  new string[] { "this sentence is close.", "several unrelated things."};

    if (_content.Length == 0)
    {
        return;
    }

    var vectors = new Vector[_content.Length];
    for (int i = 0; i < _content.Length; i++)
    {
        var content = Regex.Replace(_content[i], @"[^\u0000-\u007F]", "");
        vectors[i] = new Vector { data = GetEmbeddings(content) };
    }
    _embeddings = vectors;

    //var searchVector = GetEmbeddings(SearchTextBox.Text);
    var sentence = "this is a test.";
    var searchVector = GetEmbeddings(sentence);
    var ranking = CalculateRanking(new Vector { data = searchVector }, _embeddings);

    //FoundSentenceTextBlock.Text = "Relevant Sentence: " + _content[ranking[0]];
    Debug.WriteLine($"Adapter {_content[ranking[0]]}:");

}
```

### Implement math helper methods

Implement some common math operations that are used to help calculate the ranking for each input sentence.

```csharp
public static float CheckOverflow(double x)
{
    if (x >= double.MaxValue)
    {
        throw new OverflowException("operation caused overflow");
    }
    return (float)x;
}
public static float DotProduct(float[] a, float[] b)
{
    float result = 0.0f;
    for (int i = 0; i < a.Length; i++)
    {
        result = CheckOverflow(result + CheckOverflow(a[i] * b[i]));
    }
    return result;
}
public static float Magnitude(float[] v)
{
    float result = 0.0f;
    for (int i = 0; i < v.Length; i++)
    {
        result = CheckOverflow(result + CheckOverflow(v[i] * v[i]));
    }
    return (float)Math.Sqrt(result);
}
public static float CosineSimilarity(float[] v1, float[] v2)
{
    if (v1.Length != v2.Length)
    {
        throw new ArgumentException("Vectors must have the same length.");
    }
    int size = v1.Length;
    float m1 = Magnitude(v1);
    float m2 = Magnitude(v2);
    /*                        var normalizedList1 = raw1.Select(o => o / m1).ToArray();
                            var normalizedList2 = raw2.Select(o => o / m2).ToArray();
    */
    /*// Vectors should already be normalized.
    if (Math.Abs(m1 - m2) > 0.4f || Math.Abs(m1 - 1.0f) > 0.4f)
    {
        throw new InvalidOperationException("Vectors are not normalized.");
    }*/
    return DotProduct(v1, v2);
}
```

## Next steps

[TBD - Next steps]

## See also

[TBD - See also]
