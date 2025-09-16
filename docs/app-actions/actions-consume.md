---
title: Discover and invoke registered App Actions on Windows
description: Learn how to discover and invoke registered App Actions on Windows.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 08/11/2025

#customer intent: As a developer, I want to be able to discover and implement App Action on Windows so that I can incorporate registered actions into my app's experience.

---

# Discover and invoke registered App Actions on Windows

This article shows how to discover and invoke registered App Actions on Windows. This allows devs to incorporate actions provided by other apps into their user experience. For information on creating apps that implement app actions, see [Get started with App Actions on Windows](actions-get-started.md).

## Create a new Windows app project in Visual Studio

The App Actions feature is supported for multiple app frameworks and languages, but apps must have package identity to be able to register with the system. This walkthrough will implement a Windows App Action provider in a packaged C# WinUI 3 desktop app.

1. In Visual Studio, create a new project. 
1. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "WinUI", then select the "WinUI Blank App (Packaged)" project template.
1. Name the new project "ExampleAppActionProvider".
1. When the project loads, in **Solution Explorer** right-click the project name and select **Properties**. On the **General** page, scroll down to **Target OS** and select "Windows". For **Target OS version** and **Supported OS version**, select version 10.0.26100.0 or greater.
1. To update the project to support the Action Provider APIs, in **Solution Explorer** right-click the project name and select **Edit Project File**. Inside of **PropertyGroup**, add the following **WindowsSdkPackageVersion** element.

    ```xml
    <WindowsSdkPackageVersion>10.0.26100.75</WindowsSdkPackageVersion>
    ```

## Add a reference to the Microsoft.AI.Actions nuget package

The [Microsoft.AI.Actions](https://www.nuget.org/packages/Microsoft.AI.Actions) Nuget package enables you to initialize the app actions runtime, which provides APIs for creating the entity objects that are passed as inputs to and outputs from app actions.

1. In **Solution Explorer**, right-click the project name and select **Manage NuGet Packages...**
1. Make sure you are on the **Browse** tab and search for Microsoft.AI.Actions.
1. Select Microsoft.AI.Actions and click Install.

## Query for available app actions based on a set of inputs

The [Windows.AI.Actions.Hosting](/uwp/api/windows.ai.actions.hosting) namespace provides APIs for querying for the app actions that are currently registered with the system. The [ActionCatalog.GetActionsForInputs](/uwp/api/windows.ai.actions.hosting.actioncatalog.getactionsforinputs) method allows you to query for actions that accept a specified set of input entities. In a typical action hosting scenario, an app that works with a particular type of content will query for actions that consume that type of content, show the available actions to the user, and then, if the user selects an action, the app will invoke that action on the user's behalf.

This article will show an example implementation of the action consumer scenario. First, we will create some simple UI to display the available actions. The **ListBox** in this example is bound to a list of [ActionInstance](/uwp/api/windows.ai.actions.hosting.actioninstance) objects that will be declared in a code example later in this article. To provide a human-readable description of each action, we bind the **ListBoxItem** content to the [DisplayInfo.Description](/uwp/api/windows.ai.actions.hosting.actioninstancedisplayinfo.description) property of each **ActionInstance**.

```xaml
<ListBox Name="LBActionsList" ItemsSource="{x:Bind actionInstances}" SelectionChanged="LBActionsList_SelectionChanged">
    <ListBox.ItemTemplate >
        <DataTemplate x:DataType="actions:ActionInstance">
            <ListBoxItem Content="{x:Bind DisplayInfo.Description}" Background="LightBlue"/>
        </DataTemplate>
    </ListBox.ItemTemplate>
</ListBox>
```

In the code behind page, after declaring the list of **ActionInstance** objects, we add a helper method for querying for the app actions that take a particular set of inputs. This example will query for actions that take a single photo as an input. We create a **FileOpenPicker** to allow the user to pick an image file. Next we instantiate the [ActionRuntimeFactory](/windows/actions-source-generation/api/microsoft.ai.actions.helpers.actionruntimefactory) class, which is provided by the Microsoft.AI.Actions nuget package, by calling [CreateActionRuntime](/windows/actions-source-generation/api/microsoft.ai.actions.helpers.actionruntimefactory.createactionruntime). The action runtime is needed in order to create instances of [ActionEntity](/uwp/api/windows.ai.actions.actionentity) and it's child classes that represent the various types of inputs an action can consume. 

We call [CreatePhotoEntity](/uwp/api/windows.ai.actions.actionentityfactory.createphotoentity), passing in the path to the user-selected image file, to create a [PhotoActionEntity](/uwp/api/windows.ai.actions.photoactionentity) object. And then we create an array of **ActionEntity** objects containing the **PhotoEntity**. Actions can accept multiple combinations of different inputs, so you can add multiple entities of different types to the array.

Finally, we pass the **ActionEntity** array into **ActionCatalog.GetActionsForInputs** and assign the result to the **List** that is bound to our UI, allowing the user to select an action based on its description.

```csharp
 List<ActionInstance>? actionInstances;
 private async void GetActionsForInputs()
 {
     FileOpenPicker fileOpenPicker = new FileOpenPicker();
     var hwnd = WinRT.Interop.WindowNative.GetWindowHandle(this);
     WinRT.Interop.InitializeWithWindow.Initialize(fileOpenPicker, hwnd);
     fileOpenPicker.FileTypeFilter.Add(".png");
     var imageFile = await fileOpenPicker.PickSingleFileAsync();
     
     if(imageFile != null)
     {
         using (ActionRuntime runtime = ActionRuntimeFactory.CreateActionRuntime())
         {
             var photoEntity = runtime.EntityFactory.CreatePhotoEntity(imageFile.Path);
             var inputEntities = new ActionEntity[] { photoEntity };
             actionInstances = runtime.ActionCatalog.GetActionsForInputs(inputEntities).ToList();
             this.Bindings.Update();
         }
     }
 }
```

When we are ready to initiate the selected action, we retrieve the **ActionInstance** from the **ListBox** and call [InvokeAsync](/uwp/api/windows.ai.actions.hosting.actioninstance.invokeasync) to invoke the action. When the invocation is complete, get the output from the action by using the [Context](/uwp/api/windows.ai.actions.hosting.actioninstance.context) property of the **ActionInstance** to get an instance of [ActionInvocationContext](/uwp/api/windows.ai.actions.actioninvocationcontext) and then call [GetOutputEntities](/uwp/api/windows.ai.actions.actioninvocationcontext.getoutputentities) to get the list of output entities. In this example, we expect the first output to be a **PhotoActionEntity**, so we validate that the output exists and is of kind **ActionEntityKind.Photo**, and then access the path of the returned photo.

```csharp
private void LBActionsList_SelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (LBActionsList.SelectedItem != null)
    {
        var actionInstance = (ActionInstance)LBActionsList.SelectedItem;

        await actionInstance.InvokeAsync();
        var outputs = actionInstance.Context.GetOutputEntities();

        if (outputs.Length > 0 && outputs.First().Entity.Kind == ActionEntityKind.Photo)
        {
            var outputPhotoEntity = (PhotoActionEntity)outputs.First().Entity;
            var outputPhotoPath = outputPhotoEntity.FullPath;
            // Do something with the output photo
        }
    }
}
```
