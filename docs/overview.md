---
title: Use local AI with Microsoft Foundry on Windows
description: Learn about how you can use local AI models and APIs in your Windows applications using Microsoft Foundry on Windows - Windows AI APIs, Foundry Local, and Windows ML.
ms.date: 01/13/2026
ms.topic: overview
no-loc: [Microsoft Foundry on Windows, AI APIs, AI Toolkit, Windows ML, Foundry Local, ONNX Runtime]
ms.custom: [copilot-learning-hub]
---

# Use local AI with Microsoft Foundry on Windows

**Microsoft Foundry on Windows** is the premier solution for developers looking to integrate local AI capabilities into their Windows apps.

Microsoft Foundry on Windows provides developers with...

* **[Ready-to-use AI models and APIs](#ready-to-use-ai-models-and-apis)** via [Windows AI APIs](./apis/index.md) and [Foundry Local](./foundry-local/get-started.md)
* **[AI inferencing framework](#using-other-models-with-windows-ml)** to run any model locally via [Windows ML](./new-windows-ml/overview.md)

Regardless of whether you're new to AI, or an experienced Machine Learning (ML) expert, Microsoft Foundry on Windows has something for you.

:::image type="content" source="images/ms-foundry-on-windows-overview.png" alt-text="A diagram showing the various components that comprise Microsoft Foundry on Windows (Windows AI APIs, Foundry Local, and Windows ML).":::

## Ready-to-use AI models and APIs

Your app can effortlessly use the following local AI models and APIs in less than an hour. Distribution and runtime of the model files is handled by Microsoft, and the models are shared across apps. Using these models and APIs only takes a handful of lines of code, zero ML-expertise needed.

| Model type or API | What is it | Options and supported devices |
|--|--|--|
| **Large Language Models (LLMs)** | Generative text models | Phi Silica via AI APIs (supports fine-tuning) or 20+ OSS LLM models via Foundry Local<br/><br/>See [Local LLMs](./apis/local-llms.md) to learn more. |
| **Image Description** | Get a natural-language text description of an image | [Image Description via AI APIs](./apis/image-description.md) (Copilot+ PCs) |
| **Image Foreground Extractor** | Segment the foreground of an image | [Image Foreground Extractor via AI APIs](./apis/image-foreground-extractor.md) (Copilot+ PCs) |
| **Image Generation** | Generate images from text | [Image Generation via AI APIs](./apis/image-generation.md) (Copilot+ PCs) |
| **Image Object Erase** | Erase objects from images | [Image Object Erase via AI APIs](./apis/image-object-erase.md) (Copilot+ PCs) |
| **Image Object Extractor** | Segment specific objects in an image | [Image Object Extractor via AI APIs](./apis/image-object-extractor.md) (Copilot+ PCs) |
| **Image Super Resolution** | Increase the resolution of images | [Image Super Resolution via AI APIs](./apis/image-super-resolution.md) (Copilot+ PCs) |
| **Semantic Search** | Semantically search text and images | [App Content Search via AI APIs](./apis/app-content-search.md) (Copilot+ PCs) |
| **Speech Recognition** | Convert speech to text | Whisper via Foundry Local or Speech Recognition via Windows SDK<br/><br/>See [Speech Recognition](./apis/speech-recognition.md) to learn more. |
| **Text Recognition (OCR)** | Recognize text from images | [OCR via AI APIs](./apis/text-recognition.md) (Copilot+ PCs) |
| **Video Super Resolution (VSR)** | Increase the resolution of videos | [Video Super Resolution via AI APIs](./apis/video-super-resolution.md) (Copilot+ PCs) |

## Using other models with Windows ML

You can use a wide variety of models from Hugging Face or other sources, or even train your own models, and run those locally on Windows 10+ PCs using [Windows ML](./new-windows-ml/overview.md) *(model compatibility and performance will vary based on device hardware)*.

See [find or train models for use with Windows ML](./new-windows-ml/models.md) to learn more.

## Which option to start with

Follow this decision tree to select the best approach for your application and scenario:

1. Check if the built-in **[Windows AI APIs](./apis/get-started.md)** cover your scenario and you're targeting Copilot+ PCs. This is the fastest path to market with minimal development effort.

2. If Windows AI APIs don't have what you need, or you need to support Windows 10+, consider **[Foundry Local](./foundry-local/get-started.md)** for LLM or voice-to-text scenarios.

3. If you need custom models, want to leverage existing models from Hugging Face or other sources, or have specific model requirements that aren't covered by the above options, **[Windows ML](./new-windows-ml/get-started.md)** gives you the flexibility to find or train your own models.

Your app can also use a combination of all three of these technologies.

## Technologies available for local AI

The following technologies are available in Microsoft Foundry on Windows:

| &nbsp; | Windows AI APIs | Foundry Local | Windows ML |
|--|--|--|--|
| **What is it** | Ready-to-use AI models and APIs across a variety of task types, optimized for Copilot+ PCs | Ready-to-use LLMs and voice-to-text models | ONNX Runtime framework for running models you find or train |
| **Supported devices** | Copilot+ PCs | All Windows 10+ PCs and cross-platform<br/><br/>*(Performance varies based on available hardware, not all models available)* | All Windows 10+ PCs, and cross-platform via open-source ONNX Runtime<br/><br/>*(Performance varies based on available hardware)* |
| **Model types and APIs available** | [LLM](./apis/phi-silica.md)<br/>[Image Description](./apis/image-description.md)<br/>[Image Foreground Extractor](./apis/image-foreground-extractor.md)<br/>[Image Generation](./apis/image-generation.md)<br/>[Image Object Erase](./apis/image-object-erase.md)<br/>[Image Object Extractor](./apis/image-object-extractor.md)<br/>[Image Super Resolution](./apis/image-super-resolution.md)<br/>[Semantic Search](./apis/app-content-search.md)<br/>[Text Recognition (OCR)](./apis/text-recognition.md)<br/>[Video Super Resolution](./apis/video-super-resolution.md) | LLMs (multiple)<br/>voice-to-text<br/><br/>[Browse 20+ available models](https://www.foundrylocal.ai/models) | [Find or train your own models](./new-windows-ml/models.md) |
| **Model distribution** | Hosted by Microsoft, acquired at runtime, and shared across apps | Hosted by Microsoft, acquired at runtime, and shared across apps | Distribution handled by your app (app libraries can [share models across apps](./new-windows-ml/model-catalog/overview.md)) |
| **Learn more** | [Read the AI APIs docs](./apis/index.md) | [Read the Foundry Local docs](./foundry-local/get-started.md) | [Read the Windows ML docs](./new-windows-ml/overview.md) |

Microsoft Foundry on Windows also includes developer tooling such as [AI Toolkit for Visual Studio Code](./toolkit/index.md) and [AI Dev Gallery](./ai-dev-gallery/index.md) that will help you be successful building AI capabilities.

**[AI Toolkit for Visual Studio Code](./toolkit/index.md)** is a VS Code Extension that enables you to download and run AI models locally, including access to hardware-acceleration for better performance and scale through [DirectML](./directml/dml-get-started.md). The AI Toolkit can also help you with:

- Testing models in an intuitive playground or in your application with a REST API.
- Fine-tuning your AI model, both locally or in the cloud (on a virtual machine) to create new skills, improve reliability of responses, set the tone and format of the response.
- Fine-tuning popular small-language models (SLMs), like [Phi-3](https://azure.microsoft.com/blog/introducing-phi-3-redefining-whats-possible-with-slms/) and [Mistral](https://mistral.ai/).
- Deploy your AI feature either to the cloud or with an application that runs on a device.
- Leverage hardware acceleration for better performance with AI features using DirectML. DirectML is a low-level API that enables your Windows device hardware to accelerate the performance of ML models using the device GPU or NPU. Pairing DirectML with the ONNX Runtime is typically the most straightforward way for developers to bring hardware-accelerated AI to their users at scale. Learn more: [DirectML Overview](./directml/dml.md).
- Quantize and validate a model for use on NPU by using the model conversion capabilities

## Ideas for leveraging local AI

A few ways that Windows apps can leverage local AI to enhance their functionality and user experience include:

- Apps can [use Generative AI LLM models](./apis/local-llms.md) to understand complex topics to summarize, rewrite, report on, or expand.
- Apps can [use LLM models](./apis/local-llms.md) to transform free-form content into a structured format that your app can understand.
- Apps can [use Semantic Search models](./apis/app-content-search.md) that allow users to search for content by meaning and quickly find related content.
- Apps can use natural language processing models to reason over complex natural language requirements, and plan and execute actions to accomplish the user's ask.
- Apps can use image manipulation models to intelligently modify images, erase or add subjects, upscale, or generate new content.
- Apps can use predictive diagnostic models to help identify and predict issues and help guide the user or do it for them.

## Using Cloud AI Models

If using local AI features isn't the right path for you, [using Cloud AI models and resources](./cloud-ai.md) can be a solution.

## Use Responsible AI practices

Whenever you are incorporating AI features in your Windows app, we **highly** recommend following the [Developing Responsible Generative AI Applications and Features on Windows](rai.md) guidance.
