---
title: Overview for AI Dev Gallery app
description: The AI Dev Gallery app offers interactive samples powered by local AI models, including Windows AI Foundry, as well as the ability to download and run models from Hugging Face or GitHub.
ms.date: 05/19/2025
ms.topic: overview
no-loc: [AI Dev Gallery]
#customer intent: As a Windows developer, I want to learn about the AI Dev Gallery so that I can use it to access samples and integrate AI capabilities in my own app.
---

# What is the AI Dev Gallery?

AI Dev Gallery is an [open-source app](https://github.com/microsoft/ai-dev-gallery/) for Windows developers aiming to integrate AI capabilities into their apps and projects.

The app contains:

- Over 25 interactive samples powered by local AI models, including samples for all the Windows AI APIs.
- A user-friendly interface to explore, download, and run models from Hugging Face and GitHub that can leverage your PC's NPU, CPU, or GPU depending on your device capabilities.
- The capability to view the C# source code and seamlessly export each sample to a standalone Visual Studio project.

> [!div class="nextstepaction"]
> [Install AI Dev Gallery](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

Visit the [AI Dev Gallery repo on GitHub](https://github.com/microsoft/ai-dev-gallery/?tab=readme-ov-file#-getting-started) for instructions on how to install without using the Microsoft Store.

:::image type="content" source="../images/ai-dev-gallery.png" alt-text="A screenshot of the AI Dev Gallery app":::

## Prerequisites

- Windows 10 version 10.0.17763.0 or later
- [Visual Studio 2022 or later](https://visualstudio.microsoft.com/downloads/)
- [Windows Application Development workload](/windows/apps/get-started/start-here) in Visual Studio
- Some AI models might require a minimum amount of dedicated GPU memory to function properly

## Interact with samples powered by local AI models

AI Dev Gallery includes a selection of interactive AI-powered samples that demonstrate a wide range of functionalities. These samples cover diverse use cases, from natural language processing to image recognition, enabling developers to explore AI's potential in a hands-on, offline environment.

:::image type="content" source="../images/ai-dev-gallery-sample.png" alt-text="A screenshot of the AI Dev Gallery app using an AI-Generated text sample":::

## View and export sample source code

AI Dev Gallery allows developers to access the source code for every sample, making it quick to implement similar AI scenarios in your own project.

The app also enables exporting this code into a new standalone Visual Studio solution, containing only the specific code and model files required for the selected sample.

:::image type="content" source="../images/ai-dev-gallery-source-code.png" alt-text="A screenshot of the AI Dev Gallery app displaying the source code for a Translate Text sample":::

## Experiment with different AI models

Developers can select from a curated list of AI models or download new ones from Hugging Face. A dedicated model section enables you to learn more about the capabilities of your favorite models.

:::image type="content" source="../images/ai-dev-gallery-model-summary.png" alt-text="A screenshot of the AI Dev Gallery app displaying a model summary for Phi 3.5 Mini":::

## Responsible AI

It is of utmost importance to adopt responsible AI practices when integrating AI into your app. The AI Dev Gallery enables users to access a variety of AI models from sources such as Hugging Face, but we cannot guarantee that these models comply with [Microsoft's Responsible AI standards](https://www.microsoft.com/ai/responsible-ai).

Users of the AI Dev Gallery are strongly encouraged to take personal accountability for adopting Responsible AI principles, comprehend the limitations and ethical implications of the models, and ensure adherence to applicable licenses when integrating models into your own app.

Learn more: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)

## Frequently Asked Questions

**Is a Microsoft account necessary to use the app?**

- No, the app does not require a Microsoft account for use.

**Can I use the app without an internet connection?**

- Yes, the app works offline since the AI models are downloaded locally. However, you will need to be online to download additional AI models from Hugging Face or GitHub. 

**What AI models are available in the app?**

- The app features popular Hugging Face models and also include models and APIs from the Windows AI Foundry. When executing a sample, you can select which model you want to use. Before downloading or using an open-source model, we strongly recommend you read its model card to better understand it.

**Is the app's source code accessible? Can I contribute new samples?**

- Yes, the app is completely open-source, and its code is accessible on GitHub

**Where can I provide feedback?**

- Provide feedback or file an issue on the [AI Dev Gallery GitHub repository](https://github.com/microsoft/ai-dev-gallery/issues).

## See also

- [AI on Windows Sample Gallery](../samples/index.md)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
- [Frequently Asked Questions about using AI in Windows apps](../faq.yml)
- [Azure AI Content Safety](https://azure.microsoft.com/products/ai-services/ai-content-safety)
