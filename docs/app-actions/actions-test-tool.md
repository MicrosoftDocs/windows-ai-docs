---
title: The App Actions Testing Playground app
description: Learn how to use the App Actions Testing Playground app to test your action provider app.
ms.topic: how-to
ms.date: 05/07/2025

#customer intent: As a developer, I want implement a Windows App Action so that I can provide a unit of functionality that can be accessed from the Windows App Actions ecosystem.

---

# App Actions Testing Playground app

The App Actions Testing Playground app allows developers to validate that their actions are being successfully registered with the system and to test the functionality of their actions. For information on how to build a Windows App Action provider app, see [Get started with Windows App Actions](actions-get-started.md).

This section will walk you through how to use the testing tool when you are ready to test your actions. Before testing, deploy your app so that your actions are registered with the system.

1. Download and install the [App Actions Testing Playground app](https://aka.ms/AppActionsTestingPlayground).
1. Launch the Windows App Actions Testing Playground app.
1. On the **Action catalog** tab, click on the entry for your action.
1. If you registered an action that supports different sets of inputs and outputs, select the version you want to test from the **Overloads** drop-down..
1. Under **Inputs**, select an entity to use as an input to your action. You can add custom entities to use when testing by clicking on the **Add an entity** on the **Action catalog** tab.
1. Click the **Run Action** button.
1. You will see your app launch. 
1. In the App Actions Testing Playground app, a modal dialog will launch showing the the response from your action. 

## Viewing your action registrations

On the **Registrations** tab of the App Actions Testing Playground app, you can view the JSON registration for all registered actions. This allows you to quickly view the details of your actions.
