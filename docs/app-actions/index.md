---
title: App Actions on Windows Overview
description: Learn about App Actions on Windows, a framework that allows Windows apps to implement and register units of behavior that can be accessed from other apps and experiences, seamlessly integrating into user workflows.
ms.topic: article
ms.date: 04/14/2025
ms.author: drewbat
author: drewbatgit
dev_langs:
- csharp
---

# App Actions on Windows Overview

App Actions on Windows are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows.

## What is an App Action?

An App Action is an atomic unit of functionality. Apps build and register actions, and then Windows or other apps can recommend registered App Actions to the user at contextually relevant times and locations within the user workflow.  

### App Action implementation

Actions can be implemented by handling URI launch activation, or by through COM activation by implementing the **IActionProvider** interface. For a walkthrough of implementing a simple app action provider using URI activation, see [Get started with App Actions on Windows](actions-get-started.md). For information on implementing an app action using COM activation for more advanced scenarios, see [Use COM activation with App Actions on Windows](actions-com-activation.md).

Apps must have package identity in order to register an app action. The MSIX package manifest provides metadata about the actions that are supported by the provider app. For more information on the app package manifest syntax for App Action registration, see [actions-provider-manifest.md](actions-provider-manifest.md).

App actions are defined using a JSON format that provides metadata about one or more actions, which includes information like the unique identifier and description for the action as well as the list of inputs and outputs that the action operates on. The JSON action definition file is packaged with the provider app as content. The path to the file within the package is specified in the app package manifest so that the system can find and ingest the action definitions. For more information on on the JSON format for declaring actions, see [Action definition JSON schema for Windows App Action providers](actions-json.md).

An *entity* is an object that an App Action operates on. App Actions take entities as inputs and can return entities as outputs. Entities are divided into subtypes to represent different types of content that an action can operate on, such Document, Photo, and Text. Each entity type has a set of properties that provide information related to each content type, such as the path or file extension of a file. Entities are expressed as JSON in the action definition JSON file to declare the inputs and outputs of an app action. A set of WinRT APIs representing entities are also available for working with entities in code. For more information, see the [Windows.AI.Actions Namespace](/uwp/api/windows.ai.actions).

## Responsible AI Notes

When building AI backed actions, it is your responsibility as the Action author to perform content moderation and abuse monitoring when it comes to entities returned to the user. For more information about Microsoft Responsible AI policies for more information, see [Microsoft Responsible AI: Principles and approach](https://www.microsoft.com/en-us/ai/principles-and-approach)

> [!NOTE]
> Consider if children should have access to the action using the ‘contentAgeRating’ property in the action definition JSON.

## Recommended scenarios for App Actions

App Actions are intended to provide atomic units of functionality that are applicable to scenarios and workflows outside of the provider app. For example, an action might translate a piece of text, or process an image. For scenarios that are entirely specific to the Windows app that implements the behavior, the recommended path is to implement a custom extensibility point with an app extension. For more information, see [Create and host an app extension](/windows/uwp/launch-resume/how-to-create-an-extension).

The following table lists some questions about the behavior you plan on implementing and recommends using a Windows App Action or a custom extensibility point based on your answer.

| Criterion | Use AppAction | Use custom extensibility point |
|-----------|---------------|--------------------------------|
| Is the functionality broadly applicable and reusable? | Use an App Action if the functionality is intended for discovery and reuse across multiple apps or contexts (e.g., file operations, printing). | Use a custom extensibility point if the functionality is specific to a particular app or scenario (e.g., app-specific settings or behavior).|
| Is the functionality expected to be extended by other apps or experiences? | Use an App Action if external developers can compose and extend the functionality. | Use a custom extensibility point if the functionality should only be extended or controlled by the provider app itself (e.g., proprietary behavior or business logic).|
| Does the functionality require dynamic discovery or context-based adaptation? | Use an App Action if the functionality is context-dependent and should be dynamically discovered at runtime (e.g., displaying context-specific commands in a UI). | Use a custom extensibility point if the functionality is static and should be hardcoded into the app’s codebase (e.g., fixed settings or features). |
| Does the functionality need to integrate with existing system tools or other app ecosystems? | Use an App Action if the functionality needs to be part of a broader ecosystem, such as a common API surface or scripting environment.| Use a custom extensibility point if the functionality is isolated or intended to work only within the specific app’s ecosystem (e.g., app-specific plugins or behavior).|
| Is the functionality meant to expose complex logic or abstract user workflows? | Use an App Action if the functionality simplifies user interaction by encapsulating complex tasks into a single, higher-level action (e.g., user-driven automation). | Use a custom extensibility point if the functionality involves lower-level or highly specific tasks that need direct, fine-grained control (e.g., low-level resource management).|
| Does the app need control over the exact behavior, parameters, and extensibility of the functionality? | Use an App Action if the functionality can operate independently of the app’s internal control and doesn’t need to follow strict app-specific protocols. | Use a custom extensibility point if the app requires tight control over the behavior, lifecycle, or parameters of the functionality (e.g., custom input validation, specific workflows). |
| Is the functionality expected to be discoverable and invoked in a uniform way across various parts of the system or other apps? | Use an App Action if a consistent, discoverable API is needed across various apps or systems (e.g., an API to manipulate files or share content). Use a custom extensibility point  if the functionality is intended to be used in a specific, non-uniform way or controlled by the app itself (e.g., custom extension points like filters or app-specific functionality). |
| Is the functionality part of a larger set of generic actions or services that span across the system? | Use an App Action if the functionality is a common service or utility that multiple apps or system components would benefit from (e.g., networking, file system actions). | Use a custom extensibility point if the functionality is localized to a specific app or service, not intended to be generalized (e.g., app-specific commands or plugins). |

