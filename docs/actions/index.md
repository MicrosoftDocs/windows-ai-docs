---
title: Windows App Actions
description: Learn about Windows App Actions, a framework that allows Windows apps to implement and register units of behavior that can be accessed from other apps and experiences, seamlessly integrating into user workflows.
ms.topic: article
ms.date: 04/14/2025
ms.author: drewbat
author: drewbatgit
dev_langs:
- csharp
---

# Windows App Actions

Windows App Actions are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows.

## What is an App Action? 

An App Action is an atomic unit of functionality. Apps build and register App Actions, and then Windows or other apps can recommend registered App Actions to the user at contextually relevant times and locations within their the user workflow.  

[TBD - Some high-level description of the app experience. Maybe screenshots of an example UI flow.]Where can Actions show up? Actions can show up in any surface that is registered as a consumer of actions.   


### App Action implementation

Actions can be implemented by handling URI launch activation, or by through COM activation by implementing the **IActionProvider** interface. Apps must have package identity in order to register with the system. The system learns about your action through the presence of a JSON file in your app package, which is referenced from your MSIX package manifest. For more information, see [Implement an Action Provider](action-provider.md).

### App Action entities

An entity is an object that an App Action operates on. App Actions take entities as inputs and can return entities as outputs. In the current release, App Actions support four entity types: File, Document, Photo, and Text. Each of these entities has a set of properties that provide relevant details, such as the path or extension of a file. For more information on entities, see []