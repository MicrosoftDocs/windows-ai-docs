---
title: Return streaming text with App Actions on Windows
description: Learn how to return streaming text with App Actions on Windows.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement an App Action for Windows so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---

# Return streaming text with App Actions on Windows

This article describes how to implement an app action on Windows that incrementally streams text as output back to the caller. This feature is useful for scenarios that leverage LLMs, which typically respond with a series of tokens that incrementally build the reply.

## Return streaming text using Microsoft.AI.Actions code generation

This section describes how to implement an action that returns streaming text using the code generation capabilities provided by the [Microsoft.AI.Actions](https://www.nuget.org/packages/Microsoft.AI.Actions) Nuget package. This feature allows you to create strongly typed classes and methods to implement actions, using .NET attributes to trigger the auto-generation of the underlying action infrastructure code at build time. For information on configuring a project to use code generation, see [Get started with App Actions on Windows](actions-get-started.md). This section builds on concepts and techniques introduced in [Get started with App Actions on Windows](actions-get-started.md). It is strongly recommended that you review the content in that article before continuing as it walks through setting up your project to implement an action.

The code examples in this section should all be added to your action provider class, which is labeled with the [ActionProviderAttribute](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.actionproviderattribute).

```csharp
[ActionProvider]
public sealed class MyActionsProvider
```

Actions are implemented in a class method that is labeled with the [WindowsActionAttribute](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.windowsactionattribute), which provides metadata about the actions such as a description and an icon. The [WindowsActionInputCombinationAttribute](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.windowsactioninputcombinationattribute) describes the inputs that are supported. For this example, the action will take a single text entity as an input. For all of the actions code generation attributes, you can look at the API reference documentation to see all of the supported properties.

The return value from our action function is a .NET **record** type that contains a single field containing a [StreamingTextEntityWriter](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.streamingtextentitywriter). This class, provided by the [Microsoft.AI.Actions.Annotations](/windows/actions-source-generation/api/microsoft.ai.actions.annotations) namespace allows you to provide a callback function through which you will return the incremental text updates. 

```csharp
[WindowsAction(Description = "Get a streaming response", Icon = "ms-resource://Files/Assets/LockScreenLogo.png", UsesGenerativeAI = false)]
[WindowsActionInputCombination(Inputs = ["message"], Description = "Get a streaming response for: '${message.Text}'")]
public GetStreamingResponse GetStreamingResponseAction(string message, InvocationContext context)
{
    return new GetStreamingResponse
    {
        StreamingText = new StreamingTextEntityWriter(
            ActionEntityTextFormat.Plain,
            (textWriter) => GetStreamingTextAsync(textWriter, message))
    };
}

public record GetStreamingResponse
{
    public required StreamingTextEntityWriter StreamingText { get; init; }
}
```

For simplicity, in this example the streaming text callback from our action will return a string constructed incrementally from a hard-coded list of strings. The [SetText](/uwp/api/windows.ai.actions.streamingtextactionentitywriter.settext) method of the [StreamingTextActionEntityWriter](/uwp/api/windows.ai.actions.streamingtextactionentitywriter) passed into the callback is called as each token is processed, passing the accumulated text each time. In this example a delay is used to simulate asynchronous responses from an LLM or web service.

Sometimes an LLM will "backtrack", replacing previously returned text with new text. In this example, we return the finalized string at the end of the method call, illustrating that you can replace the text submitted with previous calls to **SetText**.

```csharp
static string[] exampleStreamingText = new string[]
    { "This", "is", "example", "streaming", "text" };

private async Task GetStreamingTextAsync(StreamingTextActionEntityWriter textWriter, string message)
{
    var stringBuilder = new StringBuilder();

    foreach (string token in exampleStreamingText)
    {
        stringBuilder.Append(token);
        textWriter.SetText(stringBuilder.ToString());
        await Task.Delay(500);
    }

    // This call replaces the content submitted in the previous calls to SetText
    // This is useful for handling when an LLM "backtracks".
    textWriter.SetText("This is the finalized example streaming text.");
}
```

When you build you solution, the code generation feature will generate the following action registration JSON file.


```json
{
  "version": 2,
  "actions": [
    {
      "id": "ExampleAppActionProvider.MyActionsProvider.GetStreamingResponseAction",
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
        "clsid": "11112222-bbbb-3333-cccc-4444dddd5555"
      }
    }
  ]
}
```

> [!IMPORTANT]
> With the current release the Microsoft.AI.Actions code generation, you need to manually add the **allowedAppInvokers** field to action definition JSON to specify a list of AppUserModelIDs that specifies the apps that can query for your actions through a call to [GetActionsForInputs](/uwp/api/windows.ai.actions.hosting.actioncatalog.getactionsforinputs). It is recommended that apps specify the wildcard "*" to make actions visible to all apps. For more information, see [Action definition JSON schema for App Actions on Windows](actions-json.md).


### Use IAsyncEnumerable to return streaming text

The code generation framework allows you to return an [IAsyncEnumerable](/dotnet/api/system.collections.generic.iasyncenumerable-1) instead of using a **StreamingTextActionEntityWriter**. For some scenarios, this approach may be easier and faster to develop, although it doesn't support specifying a text format or replacing previously returned text.

For this technique, the record returned by our **GetStreamingResponse** helper method should be an **IAsyncEnumerable** of type **string**. 

```csharp
public record GetStreamingResponse
{
    public required IAsyncEnumerable<string> StreamingText { get; init; }
}
```

The method that implements our action will simply return a new instance of the record type.

```csharp
[WindowsAction(Description = "Get a streaming response", Icon = "ms-resource://Files/Assets/LockScreenLogo.png", UsesGenerativeAI = false)]
[WindowsActionInputCombination(Inputs = ["message"], Description = "Get a streaming response for: '${message.Text}'")]
public GetStreamingResponse GetStreamingResponseAction(string message, InvocationContext context)
{
    return new GetStreamingResponse
    {
        StreamingText = GetStreamingTextAsync(message)
    };
}
```

Finally, our placeholder method for returning the streaming text tokens should return an **IAsyncEnumerable**. Use the [yield](/dotnet/csharp/language-reference/statements/yield) statement to return each incremental string token.

```csharp
static string[] exampleStreamingText = new string[]
    { "This", "is", "example", "streaming", "text" };

private async IAsyncEnumerable<string> GetStreamingTextAsync(string message)
{

    foreach (string token in exampleStreamingText)
    {
        yield return token;
        await Task.Delay(500);
    }
}
```

## Streaming text with IActionProvider

This section describes how to build the same action that was shown in the previous section, but for apps that choose to manually implement the [IActionProvider](/uwp/api/windows.ai.actions.provider.iactionprovider) interface instead of using the automatic code generation capabilities provided by the Microsoft.AI.Actions Nuget package. This section builds on concepts and techniques introduced in [Manually implement IActionProvider](actions-iactionprovider-manual.md). It is strongly recommended that you review the content in that article before continuing as it walks through setting up your project to implement an action.

Using **IActionProvider** directly means that you need to manually create your action definition JSON file. The following example JSON file defines an action that takes a single text entity as an input and returns a streaming text entity as an output. It's important to note the **id** field for the action since this will be used in our action provider code to determine which action is being called.

```json
{
  "version": 3,
  "actions": [
    {
      "id": "ExampleActionProvider.MyActionProvider.GetStreamingResponseAction",
      "description": "Get a streaming response",
      "icon": "ms-resource://Files/Assets/LockScreenLogo.png",
      "usesGenerativeAI": false,
      "allowedAppInvokers": ["*"],
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
        "clsid": "11112222-bbbb-3333-cccc-4444dddd5555"
      }
    }
  ]
}

```

As described in [Manually implement IActionProvider](actions-iactionprovider-manual.md) the system invokes actions by calling the [InvokeAsync](/uwp/api/windows.ai.actions.provider.iactionprovider.invokeasync) method of your **IActionProvider** implementation. This example uses a helper class that returns a [Task](/dotnet/api/system.threading.tasks.task), which is then converted to an **IAsyncAction** with a call to **AsAsyncAction** extension method.

In the **InvokeAsyncHelper** method, we check the ID of the invoked action to make sure it is `ExampleActionProvider.MyActionProvider.GetStreamingResponseAction`. If so, we get the text entity input from the [ActionInvocationContext](/uwp/api/windows.ai.actions.actioninvocationcontext) object passed in by the system. Next we initialize a new [StreamingTextActionEntityWriter](/uwp/api/windows.ai.actions.streamingtextactionentitywriter) object using the factory method [ActionEntityFactory.CreateStreamingTextActionEntityWriter](/uwp/api/windows.ai.actions.actionentityfactory.createstreamingtextactionentitywriter). Then we run an asynchronous task to begin sending streaming text updates back to the caller. Finally, using the **ActionInvocationContext** object, we set the result of the task to [ActionInvocationResult.Success](/uwp/api/windows.ai.actions.actioninvocationresult) and set the output entity to the [StreamingTextActionEntity](/uwp/api/windows.ai.actions.streamingtextactionentity) returned by the [StreamingTextActionEntityWriter.ReaderEntity](/uwp/api/windows.ai.actions.streamingtextactionentitywriter.readerentity) property.

```csharp
public IAsyncAction InvokeAsync(ActionInvocationContext context)
{
    return InvokeAsyncHelper(context).AsAsyncAction();
}

async Task InvokeAsyncHelper(ActionInvocationContext context)
{
    NamedActionEntity[] inputs = context.GetInputEntities();

    var actionId = context.ActionId;
    switch (actionId)
    {
        case "ExampleActionProvider.MyActionProvider.GetStreamingResponseAction":

            TextActionEntity entity = (TextActionEntity)(inputs.First().Entity);
            string message = entity.Text;

            var streamingTextWriter = context.EntityFactory.CreateStreamingTextActionEntityWriter(ActionEntityTextFormat.Plain);

            _ = Task.Run(async () => await GetStreamingTextAsync(streamingTextWriter, message));
            
            context.Result = ActionInvocationResult.Success;
            context.SetOutputEntity("StreamingText", streamingTextWriter.ReaderEntity);

            break;
        default:
            break;

    }

}
```

As in the code generation example in the previous section, the streaming text callback from our action will use tokens from a hard-coded list of strings to simulate an incremental response from an LLM. As each string in the list is processed, the accumulated string is passed into the [SetText](/uwp/api/windows.ai.actions.streamingtextactionentitywriter.settext) method of the [StreamingTextActionEntityWriter](/uwp/api/windows.ai.actions.streamingtextactionentitywriter). In this example a delay is used to simulate asynchronous responses from an LLM or web service. At the end of the method, the finalized string is passed to **SetText** to illustrate the scenario where an LLM backtracks and replaces previously returned text.

```csharp
static string[] exampleStreamingText = new string[]
{ "This", "is", "example", "streaming", "text" };

private async Task GetStreamingTextAsync(StreamingTextActionEntityWriter textWriter, string message)
{
    var stringBuilder = new StringBuilder();

    foreach (string token in exampleStreamingText)
    {
        stringBuilder.Append(token);
        textWriter.SetText(stringBuilder.ToString());
        await Task.Delay(1000);
    }

    textWriter.SetText("This is the finalized example streaming text.");
}
```

