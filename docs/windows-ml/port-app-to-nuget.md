---
author: QuinnRadich
title: Port an existing WinML app to NuGet package
description: Learn how to port an existing WinML desktop application to use the redistributable NuGet package.
ms.author: quradic
ms.date: 5/19/2020
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, NuGet
ms.localizationpriority: medium
---

# Port an existing WinML app to NuGet package (C++) 

In this tutorial, we’ll take an existing WinML desktop application and port it to use the [redistributable NuGet package](https://www.nuget.org/packages/Microsoft.AI.MachineLearning/). 

## Prerequisites

* A WinML application. If you are creating a new application, see Tutorial: Create a Windows Machine Learning Desktop application (C++) 
* Windows 8.1 or higher 
* Visual Studio 2019 (or Visual Studio 2017, version 15.7.4 or later)

## Add the NuGet Package to your project

In the Visual Studio project for your existing application, navigate to the the Solution explorer and select **Manage NuGet Packages for Solution**. Choose the `Microsoft.AI.MachineLearning` NuGet package. Ensure you're adding to the correct project, and press **Install**.

Next, build your solution again. The C++/WinRT toolkit will parse the new headers and metadata from the `Microsoft.AI.MachineLearning` NuGet package, avoiding confusion in the next step.

## Include the new header

For best practices, you should add a control flag to enable your app to swithc back and forth between using in-box Windows ML and the NuGet package.

```c++
#ifdef USE_WINML_NUGET
#include “winrt/Microsoft.AI.MachineLearning.h” 
#endif
```

## Change the namespace

Next, allow the `Windows::AI::Machinelearning` to switch over to the `Microsoft::AI::MachineLearning` namespace using a control flag. By making this change, your code will automatically use the NuGet package if applicable.

```c++
#ifdef USE_WINML_NUGET 

Using namespace Microsoft::AI::MachineLearning 

#else 

Using namespace Windows::AI::MachineLearning 

#endif 
```

## Change the Preprocessor Definitions

Now, right-click on the project in the **Solution Explorer** and select **Properties**. In the **Properties** window, choose the **Preprocessor** page. Edit the **Preprocessor Definitions**, and change it to `USE_WINML_NUGET:_DEBUG`.

## Build and run

Your application now successfully uses the WinML NuGet Package.
