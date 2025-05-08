---
title: Windows App Actions Test Tool
description: Learn how to use the Windows App Actions Test Tool to test your action provider app.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 05/07/2025

#customer intent: As a developer, I want implement a Windows App Action so that I can provide a unit of functionality that can be accessed from the Windows App Actions ecosystem.

---

# Windows App Actions Test Tool

The Windows App Actions Test Tool allows developers to validate that their Windows App Action provider app is successfully registering with the system and to test the functionality of their actions. For information on how to build a Windows App Action provider app, see [Get started with Windows App Actions](get-started.md).

[TBD - Screenshots of the test tool running this action would be nice to have here]

1. In **Solution Explorer**, right-click the project icon and select **Deploy** to deploy your app, registering your action with the system.
1. Download and install the Windows App Actions Test Tool [TBD - download link].
1. Launch the Windows App Actions Test Tool.
1. On the **Action catalog** tab, click on the entry for your action. If you followed the steps in this walkthrough, the action will be named "ExampleActionProvider.WindowsActionHandler.SendMessage" and the description will be "Send a message"
1. Since the provider app only implements one overload of the send message action, "Send message '${message.Text}' will automatically be selected in the **Overloads**.
1. Under **Inputs**, make sure that "Sample text entity" is selected in in the **message** drop-down.
1. Click the **Run Action** button.
1. You will see your app launch. [TBD - Should we have some best practice guidance for modifying the template to *not* launch the Window?]
1. In the Windows App Actions Test Tool, a modal dialog will launch showing the "Hello world" message response from your action. 