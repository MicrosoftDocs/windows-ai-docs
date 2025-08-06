---
title: Get started with Prompt Builder in AI Toolkit for VS Code
description: Learn to use the AI Toolkit for VS Code to generate starter prompts to iterate and refine with each run of the model.
ms.date: 08/05/2025
ms.topic: how-to
no-loc: [VS Code, Visual Studio Code]
#customer intent: As a Windows developer, I want to learn how to use the AI Toolkit for Visual Studio Code to generate starter prompts for my AI models.
---

# Get started with Prompt Builder in AI Toolkit for VS Code

The AI Toolkit for VS Code (AI Toolkit) is a VS Code extension that enables you to download, test, fine-tune, and deploy AI models with your apps or in the cloud. For more information, see the [AI Toolkit overview](index.md).

One of the key features of the AI Toolkit is the **Prompt Builder**. The Prompt Builder is a tool that helps you create, edit, and test prompts for your AI models. It provides a user-friendly interface for crafting prompts and allows you to:

- Create, edit, and test your prompts 
- Create AI-generated prompts
- Generate structured output for your app using a predefined schema
- Generation code for prompt interactions based on the query and model in your workspace

> [!NOTE]
> Additional documentation and tutorials for the AI Toolkit for VS Code are available in the VS Code documentation: [AI Toolkit for Visual Studio Code](https://code.visualstudio.com/docs/intelligentapps/overview). You'll find guidance on Playground, working with AI models, fine-tuning local and cloud-based models, and more.

In this article, you'll learn how to:

> [!div class="checklist"]
> - Download, load, and work with a local Phi 3.5 Mini CPU-based small language model (SLM)
> - Create, edit, and test prompts using the local model
> - Create structured output using a predefined schema
> - Generate Python code to run the model with the provided prompts

## Prerequisites

- **VS Code** must be installed. For more information, see [Download VS Code](https://code.visualstudio.com/download) and [Getting started with VS Code](https://code.visualstudio.com/docs/introvideos/basics).

When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md).

## Install

See [Get started with AI Toolkit](toolkit-getting-started.md#install) for AI Toolkit installation instructions.

## Select the local model

In this section, you'll learn how to load the Phi 3.5 Mini CPU-based SLM model. The AI Toolkit for VS Code supports local models, which are models that run on your local machine, and cloud-based models, which are hosted in the cloud.

1. Open the **AI Toolkit** view in VS Code by selecting the **AI Toolkit** icon in the Activity Bar on the side of the window.
1. In the **AI Toolkit** view, select the **Prompt Builder** item in the Tools section of the left pane.

   :::image type="content" source="../images/ai-toolkit-prompt-builder-tools.png" alt-text="A screenshot of the AI Toolkit's Tools pane":::

1. In the **Prompt Builder** view, select the **Browse models** button to open the **Model Catalog**.
1. Search for **Phi 3.5** in the **Model Catalog**. The **Phi 3.5 Mini (CPU - Small, Fast, Accurate)** model should appear in the list in the **ONNX Models** section.
1. Select the **Add** button to add the model to your workspace. The model will be downloaded and added to the **My Models** section of the left pane.
1. After the model has finished downloading, return to the **Prompt Builder** view.

   :::image type="content" source="../images/ai-toolkit-prompt-builder.png" alt-text="A screenshot of the AI Toolkit Prompt Builder window":::

1. Enter the following prompt in the **System prompt** text box:

   ```text
   You are a professor of marine biology at a respected university. Answer the following questions as best you can.
   ```

1. Enter the following prompt in the **User prompt** text box:

   ```text
   Please provide a concise list of 10 types of marine mammals that live in the Pacific Ocean.
   ```

1. Select the **Run** button to run the model with the provided prompts.
1. The model will generate a response based on the provided prompts. The response will be displayed in the **Response** text box.

   :::image type="content" source="../images/ai-toolkit-prompt-builder-result.png" alt-text="A screenshot of the AI Toolkit Prompt Builder window with the results of a query about marine mammals displayed":::

1. If you want to refine the results, you have a few options:
   - Edit the **System prompt** or **User prompt** and run query the model again.
   - Select **Add prompt** to add a new prompt to the **User prompt** text box. This will allow you to create a more complex query with multiple prompts.
   - Select **Use Response as Assistant Prompt** to use as context to help guide the model's behavior.
   - Repeat any combination of the above steps until you get the desired results.
1. Select the **View Code** button to generate Python code that uses the SDK to run the model with the provided prompts. The generated code will be displayed in a new editor tab. The code will look similar to the following:

   ```python
    """Run this model in Python
    
    > pip install openai
    """
    from openai import OpenAI
    
    client = OpenAI(
        base_url = "http://localhost:5272/v1/",
        api_key = "unused", # required for the API but not used
    )
    
    response = client.chat.completions.create(
        messages = [
            {
                "role": "system",
                "content": "You are a professor of marine biology at a university",
            },
            {
                "role": "user",
                "content": [
                    {
                        "type": "text",
                        "text": "Please provide a concise list of 10 types of marine mammals that live in the Pacific Ocean.",
                    },
                ],
            },
        ],
        model = "Phi-3.5-mini-cpu-int4-awq-block-128-acc-level-4-onnx",
        max_tokens = 256,
        frequency_penalty = 1,
    )
    
    print(response.choices[0].message.content)
   ```

1. If you want to run the generated code, make sure you have the required dependencies installed. You can install the required dependencies using pip:
1. You can run the generated code in your Python environment. For more information on installing and getting started with Python on Windows, see [Get started using Python on Windows for beginners](/windows/python/beginners).

The Prompt Builder is a powerful tool for crafting and refining prompts for your AI models. You can use it to quickly iterate on your prompts and test different variations to find the best results. For a more detailed overview of the Prompt Builder and its features, see [Prompt engineering in AI Toolkit](https://code.visualstudio.com/docs/intelligentapps/promptbuilder).

## Related content

- [AI Toolkit for Visual Studio Code documentation](https://code.visualstudio.com/docs/intelligentapps/overview)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
