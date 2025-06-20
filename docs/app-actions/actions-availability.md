---
title: Toggle availability of an App Action for Windows
description: Learn how to toggle the availability of an App Action for Windows
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement a Windows App Action so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---


# Toggle availability of an App Action for Windows

A Windows App Action provider can specify that one or more of its actions are currently unavailable. This feature enables scenarios such as requiring a login or subscription before an action is made available to the user.

## Set initial availability

You can specify the initial availability status of an app action by providing a value for the *isAvailable* field in the Action definition JSON file. The value is optional and defaults to true. The following example illustrates the usage of the *isAvailable* field to make an app action unavailable immediately after installation.

```json
"version": 2,
"actions": [
   {
     "id": "ToDoList.ToDoActionHandler.AddToList",
     "description": "Add item to your to-do list",
     "icon": "ms-resource://Files/Assets/LockScreenLogo.png",
     "usesGenerativeAI": false,
     "isAvailable": false,
    ...
```

For more information, see [Action definition JSON schema for App Actions on Windows](/windows/ai/app-actions/actions-json).

## Change the availability state at runtime

Register a change in availability state of one or more registered actions with the system by calling [ActionRuntime.SetActionAvailability](/uwp/api/windows.ai.actions.actionruntime.setactionavailability).

```csharp
void SetActionAvailability(bool actionIsAvailable)
{

    using (ActionRuntime runtime = new ActionRuntime())
    {
        runtime.SetActionAvailability("ExampleActionProvider.SendMessage", actionIsAvailable);
    }

}
```