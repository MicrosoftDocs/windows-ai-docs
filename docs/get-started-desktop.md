---
author: eliotcowley
title: Get started with Windows ML (Desktop app)
description: Create your first Desktop app with Windows ML in this step-by-step tutorial.
ms.author: elcowle
ms.date: 08/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, winml, windows ML
ms.localizationpriority: medium
---

# Get started with Windows ML (Desktop app)

In this tutorial, we'll build a desktop app that uses the SqueezeNet model to detect an object in an image. This tutorial primarily focuses on how to load and use Windows ML in your Desktop app.

## Prerequisites

* [Windows 10, build 17724 or later](https://www.microsoft.com/software-download/windowsinsiderpreviewiso)
* [Windows 10 SDK, build 17724 or later](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK)
* [Visual Studio 2017](https://developer.microsoft.com/windows/downloads)

You'll also need to add the C++/WinRT extension in Visual Studio:

1. Go to **Tools > Extensions and Updates**.

2. Select **Online** in the left pane and search for "WinRT" using the search box in the right pane.

3. Select **C++/WinRT**, click **Download**, and restart Visual Studio.

## 1. Download, build, and run the sample

1. You can get the sample from the [Windows ML samples GitHub repo](https://github.com/Microsoft/Windows-Machine-Learning/tree/RS5), either by downloading a ZIP file of the repo or by cloning the repo. If you download the samples ZIP, be sure to unzip the entire archive, not just the folder with the sample you want to build.

2. Start Microsoft Visual Studio 2017 and select **File > Open > Project/Solution**.

3. Starting in the folder where you unzipped the samples, open **Samples\\SqueezeNetObjectDetection\\Desktop\\cpp\\SqueezeNetObjectDetection.sln**. Make sure the drop-down menus in the top toolbar are set correctly (the left one should be set to **Debug**, and the right one should either be **x64** or **x86** depending on your computer's architecture). Press **Ctrl+Shift+B**, or select **Build > Build Solution** to build the sample.

4. To run the project, press **F5** or select the green play button on the top toolbar.