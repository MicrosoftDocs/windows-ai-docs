---
title: Add Streaming text output to a Windows App Action
description: Learn how to add streaming text output to a Windows App Action.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement a Windows App Action so that I can provide a unit of functionality that can be accessed from the Windows App Actions ecosystem.

---


# Add Streaming text output to a Windows App Action

A common scenario for a Windows App Action is to use LLMs to generate results based on the user input. LLMs typically return results incrementally, a few characters at a time. Windows App Actions can return results incrementally to the caller by specifying the **StreamingText** entity as its output type. This lets the system know that the results will be returned asynchronously in multiple updates.

For more information about Windows App Actions, see [Windows App Actions Overview](index.md)


## Prerequisites

- This article builds on the simple Windows App Action that is created in [Get started with Windows App Actions](get-started.md). You should follow the steps in that article and learn about the Windows App Actions VSIX Extension before continuing with this walkthrough.

## Add a Windows App Action with streaming text output

The following example defines a new action that is identical to the **SendMessage** action that is generated in the default **ActionHandler**, but in this case, the action returns streaming text.

1. Create new response type for streaming text. This type must have a single property **StreamingText** that is of type [StreamingTextEntityWriter TBD - link](). 

```csharp
public record GetStreamingResponse
{
    public required StreamingTextEntityWriter StreamingText { get; init; }
}
```

2. Add a new method, **GetStreamingResponse**, that defines the new action. This method, and its attributes are almost identical to the **SendMessage** action that has already been defined. It takes the same inputs, but the return type has been changed to the **GetStreamingResponse** type declared in the previous step. The **StreamingText** property of the response object is set to a new **StreamingTextWriterObject**. In the constructor for this type, you pass in a callback that will be called to retrieve the incremental chunks of text. The callback method **GetStreamingTextAsync** is shown in the next step.

```csharp
[WindowsAction(Description = "Get a streaming response", Icon = "ms-resource://Files/Assets/LockScreenLogo.png", UsesGenerativeAI = false)]
[InputCombination(Inputs = ["message"], Description = "Get a streaming response for: '${message.Text}'")]
public GetStreamingResponse GetStreamingResponse(string message, InvocationContext context)
{
    return new GetStreamingResponse
    {
        // StreamingText = GetStreamingTextAsync(firstName, cityOfAction)
        StreamingText = new StreamingTextEntityWriter(
            ActionEntityTextFormat.Plain,
            (textWriter) => GetStreamingTextAsync(textWriter, message))
    };
}
```

When you add this method definition and its associated attributes, the Windows App Actions VSIX Extension automatically adds the following action definition to the registration.json file.

```json
{
  "id": "ActionsTest1.WindowsActionHandler.GetStreamingResponse",
  "description": "Get a streaming response",
  "icon": "ms-resource://Files/Assets/LockScreenLogo.png",
  "usesGenerativeAI": false,
  "inputs": [
    {
      "name": "message",
      "kind": "Text"
    }
  ],
  "inputCombinations": [
    {
      "inputs": [
        "message"
      ],
      "description": "Get a streaming response for: '${message.Text}'"
    }
  ],
  "outputs": [
    {
      "name": "StreamingText",
      "kind": "StreamingText"
    }
  ],
  "invocation": {
    "type": "COM",
    "clsid": "23869360-3bc9-53b4-1f4c-581ece0fd9f6"
  }
}
```

3. Implement the **StreamingTextEntityWriter** callback. In a typical scenario, an LLM would be queried and the incremental results of that query would be returned by the callback. For simplicity, this example returns values from a static array of strings, with a short delay between each string.


```csharp
static string[] exampleStreamingText = new string[]
    { "This", "is", "example", "streaming", "text" };

private async Task GetStreamingTextAsync(StreamingTextActionEntityWriter textWriter, string message)
{

    foreach(string token in exampleStreamingText)
    {
        textWriter.SetText(token);
        await Task.Delay(500);
    }
}
```