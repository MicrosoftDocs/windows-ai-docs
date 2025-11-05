---
title: App Agents on Windows Overview
description: Learn about App Agents on Windows, a framework that allows Windows apps to implement and register AI-based agents that can be invoked to perform tasks autonomously.
ms.topic: concept-article
ms.date: 1/05/2025
dev_langs:
- csharp
---

# App Agents on Windows Overview

App Agents on Windows is a framework that allows Windows apps to implement and register AI-based agents that can be invoked to perform tasks autonomously.

## What is an App Agent?

TBD

### App Agent implementation

App agents are built using the Windows App Actions framework. App Actions on Windows are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows. An app agent is just an app action, but with a few additional requirements that make it accessible to the App Agents on Windows framework. These include:

* An agent definition manifest JSON file that provides metadata about the agent, such as a display name, description, and unique identifier.
* An app extension declaration in the app package manifest file that allows Windows to ingest the agent definition and register the agent with the system.
* A required set of inputs and output entities, with strictly enforced types and names, declared in the action definition manifest.



