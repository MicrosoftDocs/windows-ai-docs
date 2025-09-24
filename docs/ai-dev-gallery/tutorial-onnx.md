---
title: Tutorial - Use any ONNX LLM with Windows ML in the AI Dev Gallery  
description: Learn how to run ONNX-based large language models (LLMs) with Windows ML using the AI Dev Gallery app.  
ms.date: 09/23/2025  
ms.topic: tutorial  
no-loc: [AI Dev Gallery, Windows ML, ONNX Runtime GenAI]  
#customer intent: As a Windows developer, I want to use ONNX-based LLMs in the AI Dev Gallery so that I can easily test and integrate AI functionality into my apps.  
---

# Tutorial: Use any ONNX LLM with Windows ML in the AI Dev Gallery

In this tutorial, you’ll learn how to run any **ONNX-based large language model (LLM)** with **Windows ML** through the **AI Dev Gallery**—an open-source Windows application (available in the [Microsoft Store](https://aka.ms/ai-dev-gallery)) that showcases AI-powered samples.

These steps work for any LLM in the [**ONNX Runtime GenAI format**](https://github.com/microsoft/onnxruntime-genai), including:

- Models downloaded from [Hugging Face](https://onnxruntime.ai/huggingface)  
- Models converted from other frameworks using the [**AI Toolkit for Visual Studio Code**](https://learn.microsoft.com/windows/ai/toolkit/) conversion tool

---

## Step 1: Select an Interactive Sample in the AI Dev Gallery

1. Open the **AI Dev Gallery** app.  
2. Navigate to the **Samples** tab and choose a **Text** sample (for example, “Generate Text” or “Chat”).

   :::image type="content" source="../images/ai-dev-gallery-tutorial/picture1.png" alt-text="Navigate to the Samples tab and choose a Text sample":::

3. Click the **Model Selector** button to view available models for that sample.

   :::image type="content" source="../images/ai-dev-gallery-tutorial/picture2.png" alt-text="Click the Model Selector button":::

4. Select **Custom models**, then click **Open AI Toolkit’s Conversion Tool**.

    :::image type="content" source="../images/ai-dev-gallery-tutorial/picture3.png" alt-text="Click the Model Selector button":::

5. This will launch the **AI Toolkit extension** in Visual Studio Code.  
   - If you don’t have it installed, search for “AI Toolkit” in the VS Code Extensions Marketplace.

---

## Step 2: Get or Convert an ONNX LLM model

To use a model in AI Dev Gallery, it must be in the **ONNX Runtime GenAI** format. You can:  

- **Download a pre-converted model:**  
  - Browse models on [Hugging Face ONNX Models](https://onnxruntime.ai/huggingface), or
  - In AI Dev Gallery, go to **Add model → Search HuggingFace**  

    :::image type="content" source="../images/ai-dev-gallery-tutorial/picture4.png" alt-text="Navigate to the search HuggingFace option":::


- **Convert your own model:**  
  - Use the [AI Toolkit for Visual Studio Code](../toolkit/toolkit-fine-tune.md) to convert a supported model to ONNX Runtime GenAI format.  

### Currently supported models for conversion:

- **DeepSeek R1 Distill Qwen 1.5B**  
- **Phi 3.5 Mini Instruct**  
- **Qwen 2.5-1.5B Instruct**  
- **Llama 3.2 1B Instruct**  

> [!NOTE]  
> AI Toolkit conversion is in Preview and currently supports only the models listed above.

---

## Step 3: Use the ONNX model in the AI Dev Gallery

1. After conversion, return to the AI Dev Gallery **model selector window**.  
2. Click **Add model → From Disk** and provide the path to your ONNX model.  

   :::image type="content" source="../images/ai-dev-gallery-tutorial/picture5.png" alt-text="Add model from disk in AI Dev Gallery":::

   > [!NOTE]  
   > If you used AI Toolkit’s conversion tool, the converted model path should follow this format:  
   > `c:/{workspace}/{model_project}/history/{workflow}/model/model.onnx`

3. Once added, you can now select your model and use it with the interactive samples.  

   :::image type="content" source="../images/ai-dev-gallery-tutorial/picture6.png" alt-text="Custom model selected and ready to use in AI Dev Gallery":::

4. Optionally, click **Show source code** in the app to view the code that runs the model.  

   :::image type="content" source="../images/ai-dev-gallery-tutorial/picture7.png" alt-text="Show source code for the selected ONNX model sample in AI Dev Gallery":::

---

## Supported Samples in the AI Dev Gallery

These ONNX LLMs can be used with the following samples in the AI Dev Gallery:

### Text  
- Generate Text  
- Summarize Text  
- Chat  
- Semantic Kernel Chat  
- Grammar Check  
- Paraphrase Text  
- Analyze Text Sentiment  
- Content Moderation  
- Custom Parameters  
- Retrieval Augmented Generation  

### Smart Controls  
- Smart TextBox  

### Code  
- Generate Code  
- Explain Code  

---

## See also

- [Download AI Dev Gallery](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)  
- [ONNX models on Hugging Face](https://onnxruntime.ai/huggingface)  
- [AI Toolkit for Visual Studio Code](../toolkit/toolkit-fine-tune.md)  
- [AI Dev Gallery GitHub Repo](https://github.com/microsoft/ai-dev-gallery/)  

---