---
title: Position App Action UI relative to the invoking app
description: Learn how App Actions on Windows providers can detect the invoking app window position.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want position the UI for my App Action for Windows provider relative to the invoking app. 
---

# Position App Action UI relative to the invoking app

This article describes how a provider app for App Actions on Windows can determine the location of the app window that has invoked an action. This allows the provider to place any UI that supports the action near to the calling window, to provide a more seamless user experience.

For more information on implementing a provider app for App Actions on Windows, see [Get started with App Actions on Windows](actions-get-started.md).

## Declare the displaysUI JSON property

The action definition JSON file supports the **displaysUI** property for each action defined in the file. Set this value to **true**, or omit the value to use the default value of **true**, to provide a hint to the invoking app that the action may display UI elements as part of its workflow. For more information about the format of this file, see [Action definition JSON schema for App Actions on Windows](actions-json.md).

## Get the location of the invoker app window

When an action is launched, the [ActionInvocationContext.InvokerWindowId](/uwp/api/windows.ai.actions.actioninvocationcontext.invokerwindowid) property contains the [Windows.UI.WindowId](/uwp/api/windows.ui.windowid) associated with the app window that invoked the action, if it was provided by the invoker. It is optional for action invokers to specify a Window ID, which is done by calling [ActionRuntime.CreateInvocationContextWithWindowId](/uwp/api/windows.ai.actions.actionruntime.createinvocationcontextwithwindowid). The Window ID will be non-zero if it is provided by the invoker app.

For more information about Windows and Window management, see [Manage app windows](/windows/apps/develop/ui/manage-app-windows). For more information about invoking actions, see [Discover and invoke registered App Actions on Windows](actions-consume.md).

The following example shows how to retrieve the **InvokerWindowId** and convert it from a **Windows.UI.WindowId** to a **Microsoft.UI.WindowId**, which is needed to retrieve a WinUI Window. If the invoker Window ID is valid, the example gets the position of the invoker Window and moves the action provider app window to that position.

```csharp
// Convert Windows.UI.WindowId to Microsoft.UI.WindowId by creating a new WindowId with the same Value
Microsoft.UI.WindowId invokerWindowIdCast = new Microsoft.UI.WindowId { Value = context.InvokerWindowId.Value };

// Get the invoker AppWindow
AppWindow callerWindow = AppWindow.GetFromWindowId(invokerWindowIdCast);

if (callerWindow != null)
{

    // Get the caller window's position
    PointInt32 callerPosition = callerWindow.Position;

    // Move the app Window to the caller window's position
    Window.Current.AppWindow.Move(callerPosition);
}
```