---
title: App Actions on Windows Overview
description: Learn about App Actions on Windows, a framework that allows Windows apps to implement and register units of behavior that can be accessed from other apps and experiences, seamlessly integrating into user workflows.
ms.topic: concept-article
ms.date: 04/14/2025
dev_langs:
- csharp
---

# App Actions on Windows Overview

App Actions on Windows are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows.

## What is an App Action?

An App Action is an atomic unit of functionality. Apps build and register actions, and then Windows or other apps can recommend registered actions to the user at contextually relevant times and locations within the user workflow.  

### App Action implementation

Actions can be implemented by handling URI launch activation, or by through COM activation by implementing the **IActionProvider** interface. For a walkthrough of implementing a simple app action provider using URI activation, see [Get started with App Actions on Windows](actions-get-started.md).

Apps must have package identity in order to register an app action. The MSIX package manifest provides metadata about the actions that are supported by the provider app. For more information on the app package manifest syntax for App Action registration, see [actions-provider-manifest.md](actions-provider-manifest.md).

Actions are defined using a JSON format that provides metadata about one or more actions, which includes information like the unique identifier and description for the action as well as the list of inputs and outputs that the action operates on. The JSON action definition file is packaged with the provider app as content. The path to the file within the package is specified in the app package manifest so that the system can find and ingest the action definitions. For more information on on the JSON format for declaring actions, see [Action definition JSON schema for Windows App Action providers](actions-json.md).

An *entity* is an object that an App Action operates on. Actions take entities as inputs and can return entities as outputs. Entities are divided into subtypes to represent different types of content that an action can operate on, such Document, Photo, and Text. Each entity type has a set of properties that provide information related to each content type, such as the path or file extension of a file. Entities are expressed as JSON in the action definition JSON file to declare the inputs and outputs of an app action. A set of WinRT APIs representing entities are also available for working with entities in code. For more information, see the [Windows.AI.Actions Namespace](/uwp/api/windows.ai.actions).

## Responsible AI Notes

When building AI backed actions, it is your responsibility as the Action author to perform content moderation and abuse monitoring when it comes to entities returned to the user. For more information about Microsoft Responsible AI policies for more information, see [Microsoft Responsible AI: Principles and approach](https://www.microsoft.com/en-us/ai/principles-and-approach)

> [!NOTE]
> Consider if children should have access to the action using the 'contentAgeRating' property in the action definition JSON.

## Recommended scenarios for App Actions

App Actions are intended to provide atomic units of functionality that are applicable to scenarios and workflows outside of the provider app. For example, an action might translate a piece of text, or process an image. For scenarios that are entirely specific to the Windows app that implements the behavior, the recommended path is to implement a custom extensibility point with an app extension. For more information, see [Create and host an app extension](/windows/uwp/launch-resume/how-to-create-an-extension).

The following list describes some kinds of functionality that can make good candidates for implementing as an action.

* The functionality broadly applicable and reusable. The functionality is intended for discovery and reuse across multiple apps or contexts (e.g., file operations, printing).
* Other apps can compose and extend the functionality.
* The functionality is context-dependent and should be dynamically discovered at runtime (e.g., displaying context-specific commands in a UI).
* The functionality integrates with existing system tools or other app ecosystems.
* The functionality simplifies user interaction by encapsulating complex tasks into a single, higher-level action (e.g., user-driven automation).
* The functionality can operate independently of the app's internal control and doesn't need to follow strict app-specific protocols.
* The functionality expected to be discoverable and invoked in a uniform way across various parts of the system or other apps (e.g., an API to manipulate files or share content).


