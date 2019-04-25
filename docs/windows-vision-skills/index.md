---
author: eliotcowley
title: Windows Vision Skills
description: Learn about the Windows Vision Skills APIs.
ms.author: elcowle
ms.date: 4/25/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, windows vision skills
ms.localizationpriority: medium
---

# Windows Vision Skills (Preview)

> [!NOTE]
> Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

Implementing and integrating efficient machine learning and computer vision solutions is a hard task for developers. The industry is moving at a fast pace and the amount of custom-tailored solutions coming out makes it strenuous for application developers to keep up. Existing APIs and lower-level frameworks add a steep learning curve before developers can leverage them effectively.

The **Windows Vision Skills** framework is meant to make it easier to utilize computer vision. It standardizes the way computer vision modules are put to use within a Windows application, running on the local device. It abstracts complexities into a single programming paradigm with standardized primitives, helping developers focus on building awesome computer vision applications.

![Diagram of how Windows Vision Skills fits into the development stack; starts with the bottom layer (GPU, CPU, VPU, etc); on top of that are hardware acceleration frameworks (DirectX, DirectML, and others); the next layer is the Windows Vision Skills API, consisting of Windows APIs and third-party frameworks; and the top layer consists of UWP, .NET Core, and Win32 applications](../images/vision-skills-diagram2-wide.png)

The implementation that contains the complex details is encapsulated by an extensible WinRT API that inherits the base classes and interfaces in the [Microsoft.AI.Skills.SkillInterfacePreview](https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview) namespace. This API can be ingested by all types of Windows apps (UWP, .NET Core, and Win32). This framework is open for all developers to build on top of.

* [Get the NuGet package](https://www.nuget.org/packages/Microsoft.AI.Skills.SkillInterfacePreview/)

## What is a *skill*?

In the context of Windows Vision Skills, a skill is a streamlined, modular piece of code that processes input and produces output. A skill can encapsulate simple functionalities like edge detection with a single-purpose micro-skill, or rich sets of functionalities forming a scenario skill to address a complex problem like skeletal detection.

## Benefits

- **Simple integration**: Skills make it easy to add features to your application with out-of-the-box APIs that don’t require any machine learning or computer vision expertise, or prior Windows knowledge of low-level APIs.

- **Abstracting hardware acceleration**: The Windows Vision Skills framework queries the hardware assets and provides OS provisions that allow developers to make efficient compute decisions at runtime.

- **Interoperability**: The framework works with OS interfaces and assets such as image primitives from cameras, photos, and video, and it can be used in conjunction with the [Windows Machine Learning APIs](../windows-ml/index.md).

- **NuGet packages**: Windows Vision Skills is strongly versioned to ease iteration without breaking existing applications. They are easy to ingest, easy to update, and they preserve intellectual property through licensing.

- **Extensibility**: The framework can be easily extended to work with existing machine learning frameworks and libraries such as **OpenCV**.

- **Modularity**: Skills can be pieced together in succession within an application just like a recipe to address a complex scenario. Skills can also be bundled together in a single package by the developer.

While this preview focuses on vision-oriented scenarios and primitives, the API is meant to accommodate a wide range of input and output variables that enable audio processing, text processing, and more.

## See also

- [Windows Vision Skills NuGet packages](https://www.nuget.org/profiles/VisionSkills)

- [Samples on GitHub](https://github.com/Microsoft/WindowsVisionSkillsPreview)

- [API reference](https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview)

[!INCLUDE [help](../includes/get-help-vision.md)]
