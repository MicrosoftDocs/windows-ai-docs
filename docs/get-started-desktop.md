---
author: eliotcowley
title: Windows Machine Learning for Desktop (C++) tutorial
description: This tutorial shows how to build a simple Windows ML application for desktop.
ms.author: elcowle
ms.date: 08/03/2018
ms.topic: article
ms.prod: windows
ms.technology: desktop
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Tutorial: Create a Windows Machine Learning Desktop application (C++)

Windows ML APIs can be leveraged to easily interact with machine learning models within C++ desktop (Win32) applications. Using the three steps of loading, binding, and evaluating, your application can benefit from the power of machine learning.

![Load -> Bind -> Evaluate](images/load-bind-evaluate.png)

We will be creating the SqueezeNet Object Detection sample, which is available on [GitHub](https://github.com/Microsoft/Windows-Machine-Learning/tree/RS5/Samples/SqueezeNetObjectDetection/Desktop/cpp). You can download the complete sample if you want to see what it will be like when you finish.

In this tutorial, you'll learn how to:

> [!div class="checklist"]
> * All tutorials include a list summarizing the steps to completion
> * Each of these bullet points align to a key H2
> * Use these green checkboxes in a tutorial
<!---Required:
The outline of the tutorial should be included in the beginning and at the end of every tutorial. These will align to the **procedural** H2 headings for the activity. You do not need to include all H2 headings. Leave out the prerequisites, clean-up resources and next steps--->

<!---Avoid notes, tips, and important boxes. Readers tend to skip over them. Better to put that info directly into the article text.--->

## Prerequisites

* [Visual Studio 2017, version 15.7.4 or later](https://developer.microsoft.com/windows/downloads
)
* [Windows 10, build 17728 or later](https://www.microsoft.com/software-download/windowsinsiderpreviewiso
)
* [Windows SDK, build 17723 or later](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK
)
* Visual Studio extension for C++/WinRT
    1. In Visual Studio, select **Tools > Extensions and Updates**.
    2. Select **Online** in the left pane and search for "WinRT" using the search box on the right.
    3. Select **C++/WinRT**, click download, and close Visual Studio.
    4. Follow the installation instructions, then re-open Visual Studio.

## Procedure 1

<!---Required:
Tutorials are prescriptive and guide the customer through an end-to-end procedure. Make sure to use specific naming for setting up accounts and configuring technology.
Don't link off to other content - include whatever the customer needs to complete the scenario in the article. For example, if the customer needs to set permissions, include the permissions they need to set, and the specific settings in the tutorial procedure. Don't send the customer to another article to read about it.
In a break from tradition, do not link to reference topics in the procedural part of the tutorial when using cmdlets or code. Provide customers what they need to know in the tutorial to successfully complete the tutorial.
For portal-based procedures, minimize bullets and numbering.
For the CLI or PowerShell based procedures, don't use bullets or numbering.
--->

Include a sentence or two to explain only what is needed to complete the procedure.

1. In Visual Studio, select **File > New > Project** to open the **New Project** window.
2. In the left pane, select **Installed > Visual C++ > Windows Desktop**, and in the middle, select **Windows Console Application (C++/WinRT)**.
3. Give your project a **Name** and **Location**, then click **OK**.
1. Step four of the procedure

## Procedure 2

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step one of the procedure
1. Step two of the procedure
1. Step three of the procedure

## Procedure 3

Include a sentence or two to explain only what is needed to complete the procedure.
<!---Code requires specific formatting. Here are a few useful examples of commonly used code blocks. Make sure to use the interactive functionality where possible.
For the CLI or PowerShell based procedures, don't use bullets or numbering.--->

Here is an example of a code block for Java:

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

or a code block for Azure CLI:

    ```azurecli-interactive 
    az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
    ```
    or a code block for Azure PowerShell:

    ```azurepowershell-interactive
    New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
    ```

## Clean up resources

If you're not going to continue to use this application, delete <resources> with the following steps:

1. From the left-hand menu...
2. ...click Delete, type...and then click Delete

<!---Required:
To avoid any costs associated with following the tutorial procedure, a Clean up resources (H2) should come just before Next steps (H2)
--->

## Next steps

Advance to the next article to learn how to create...
> [!div class="nextstepaction"]
> [Next steps button](contribute-get-started-mvc.md)

<!--- Required:
Tutorials should always have a Next steps H2 that points to the next logical tutorial in a series, or, if there are no other tutorials, to some other cool thing the customer can do. A single link in the blue box format should direct the customer to the next article - and you can shorten the title in the boxes if the original one doesn’t fit.
Do not use a "More info section" or a "Resources section" or a "See also section". --->