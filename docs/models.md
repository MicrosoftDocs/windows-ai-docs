---
title: Use Machine Learning models in your Windows app
description: Learn more about using Machine Learning models in your Windows app.
ms.author: mattwoj
author: mattwojo
ms.reviewer: mbattist
ms.date: 05/21/2024
ms.topic: overview
---

# Use Machine Learning models in your Windows app

This guide will help App Developers new to working with Artificial Intelligence (AI) and Machine Learning (ML) models by addressing common questions, sharing basic concepts and resources, and offering recommendations on how to use AI and ML models in a Windows app.

**Machine Learning (ML)** is a branch of Artificial Intelligence (AI) that enables computers to learn from data and make predictions or decisions.

**ML models** are algorithms that can be trained on data and then deployed to perform various tasks, such as content generation, reasoning over content, image recognition, natural language processing, sentiment analysis, and much more.

## How can Windows applications leverage ML models?

A few ways that Windows applications can leverage ML models to enhance their functionality and user experience, include:

- Apps can use Generative AI models to understand complex topics to summarize, rewrite, report on, or expand.
- Apps can use models that transform free-form content into a structured format that your app can understand.
- Apps can use Semantic Search models that allow users to search for content by meaning and quickly find related content.
- Apps can use natural language processing models to reason over complex natural language requirements, and plan and execute actions to accomplish the user's ask.
- Apps can use image manipulation models to intelligently modify images, erase or add subjects, upscale, or generate new content.
- Apps can use predictive diagnostic models to help identify and predict issues and help guide the user or do it for them.

### ML Models in AI-backed APIs

The **Windows AI Fabric** knits together several ways of interacting with the operating system that utilize AI. This includes ready-to-use AI-backed features and APIs, which we call the **Windows AI Library**. See [Get started using AI-backed APIs in your Windows app](./apis/index.md) for guidance on these ready-to-use features and APIs that support some of the scenarios listed above.

The Windows AI Library models run locally, directly on the Windows device, though you may also choose to use a cloud-based model via a ready-to-use API. Whether they are running a local or in the cloud, these APIs abstract away the underlying ML model so that you don't have to do any optimizing, formatting, or fine-tuning.

You may, however, want to find your own ML model to use locally on Windows. You may need to optimize this model so that it will run correctly on Windows devices or [fine-tune](fine-tuning.md) a model so that it is trained with your own customized data specific to your particular use-case or company. This article will cover some of the concepts, tools, and open source libraries to help guide you through this process.

## Running a Small Language Model locally versus a Large Language Model in the cloud

**Small Language Models (SLMs)** are designed to be compact and efficient, often trained for specific tasks or domains on smaller datasets to allow for storing and running the model locally with a quicker inference performance time. SLMs are restricted in the amount of data used to train them, not providing as extensive knowledge or complex reasoning as a Large Language Model (LLM). However, SLMs can provide a more secure and cost-effective alternative to LLMs when used locally because they require less computational power to run and improved data privacy, by keeping your chat information securely local to your device.

SLMs are more ideal for local use since running an ML model on a device means that the size must not exceed the storage and processing capability of the device running it. Most LLMs would be too large to run locally.

The Microsoft [Phi-2](https://www.microsoft.com/research/blog/phi-2-the-surprising-power-of-small-language-models/) and [Phi-3](https://azure.microsoft.com/blog/introducing-phi-3-redefining-whats-possible-with-slms/) models are examples of SLMs.

**Large Language Models (LLMs)** have been trained on huge amounts of data with a greater number of parameters, making them more complex and larger in size for storage. Due to their size, LLMs may be more capable of understanding more nuanced and complex patterns in the data, covering a broader spectrum on knowledge with the ability to work with more complex patterns. They also require more significant computational resources for both training and inference. Most LLMs would not be able to run on a local device.

The [OpenAI language models](https://platform.openai.com/docs/models) GPT-4o, GPT-4 Turbo, GPT-3.5 Turbo, DALL-E, and Whisper are all examples of LLMs.

For further guidance on the difference between using an SLM locally versus an LLM in the cloud, see [Considerations for using local versus cloud-based AI-backed APIs in your Windows app](./apis/index.md#considerations-for-using-local-versus-cloud-based-ai-backed-apis-in-your-windows-app).

## Find open source ML models on the web

**Open Source ML models** that are ready to use, and **can be customized** with your own data or preferences, are available in a variety of places, a few of the most popular include:

- [Hugging Face](https://huggingface.co/models): A hub of over 10,000 pre-trained ML models for natural language processing, powered by the Transformers library. You can find models for text classification, question answering, summarization, translation, generation, and more.
- [ONNX Model Zoo](https://onnx.ai/models/): A collection of pre-trained ML models in ONNX format that cover a wide range of domains and tasks, such as computer vision, natural language processing, speech, and more.
- [Qualcomm AI Hub](https://aihub.qualcomm.com/models): A platform that provides access to a variety of ML models and tools optimized for Qualcomm Snapdragon devices. You can find models for image, video, audio, and sensor processing, as well as frameworks, libraries, and SDKs for building and deploying ML applications on mobile devices. Qualcomm AI Hub also offers tutorials, guides, and community support for developers and researchers.
- [Pytorch Hub](https://pytorch.org/hub/): A pre-trained model repository designed to facilitate research reproducibility and enable new research. It is a simple API and workflow that provides the basic building blocks for improving machine learning research reproducibility. PyTorch Hub consists of a pre-trained model repository designed specifically to facilitate research reproducibility.
- [TensorFlow Hub](https://www.tensorflow.org/hub): A repository of pre-trained ML models and reusable components for TensorFlow, which is a popular framework for building and training ML models. You can find models for image, text, video, and audio processing, as well as transfer learning and fine-tuning.
- [Model Zoo](https://modelzoo.co/): A platform that curates and ranks the best open source ML models for various frameworks and tasks. You can browse models by category, framework, license, and rating, and see demos, code, and papers for each model.

Some model libraries are **not intended to be customized** and distributed via an app, but are helpful tools for hands-on exploration and discovery as a part of the development lifecycle, such as:

- [Ollama](https://ollama.com/library): Ollama is a marketplace of ready-to-use ML models for various tasks, such as face detection, sentiment analysis, or speech recognition. You can browse, test, and integrate the models into your app with a few clicks.
- [LM Studio](https://lmstudio.ai/): Lmstudio is a tool that lets you create custom ML models from your own data, using a drag-and-drop interface. You can choose from different ML algorithms, preprocess and visualize your data, and train and evaluate your models.

Whenever you are finding an ML model with the goal of using it in your Windows app, we **highly** recommend following the [Developing Responsible Generative AI Applications and Features on Windows](rai.md) guidance. This guidance will help you to understand governance policies, practices, and processes, identify risk, recommend testing methods, utilize safety measures like moderators and filters, and calls out specific considerations when selecting a model that is safe and responsible to work with.

## How do I optimize an ML model to run on Windows?

There are different ways to use ML models in Windows apps, depending on the type, source, and format of the models, and the type of app.

A few of the formats that you will find ML models in include:

- [**ONNX**](https://onnx.ai/): An open standard for representing and exchanging ML models across different frameworks and platforms. If you find a pre-trained ML model in ONNX format, you can use [ONNX Runtime (ORT)](https://onnxruntime.ai/) to load and run the model in your Windows app. ORT allows you to access the hardware-accelerated inference capabilities of your device and optimize the performance of your ML model. If you have a pre-trained ML model in a different format, such as PyTorch or TensorFlow, you can convert it to ONNX using a model optimization tool like [Olive](https://github.com/microsoft/OLive). For help using Olive, see [Fine-tune SLM with Microsoft Olive (Journey Series for Generative AI Application Architecture)](https://techcommunity.microsoft.com/t5/educator-developer-blog/journey-series-for-generative-ai-application-architecture-fine/ba-p/4080813). For tutorials on creating and using ONNX models, see [ONNX Tutorials on GitHub](https://github.com/onnx/tutorials). For samples demonstrating how to use ONNX models in a Windows app, see the [AI on Windows Sample Gallery](./samples/index.md).

- [**PyTorch**](https://pytorch.org/): A very popular open-source deep learning framework available with a Python and C++ interface. This will likely be the most common format your will find for ML models. If you want to use PyTorch ML models in your Windows (C# or C++) app or in a web app, you can use [TorchSharp](https://github.com/dotnet/TorchSharp) and [LibTorch](https://github.com/pytorch/pytorch/blob/main/docs/libtorch.rst), which are .NET and C++ bindings for the PyTorch library. TorchSharp and LibTorch allow you to create, load, and manipulate tensors, build and run neural networks, and save and load models using the PyTorch format. For samples, checkout [TorchSharp Examples](https://github.com/dotnet/TorchSharpExamples), [TorchScript for Deployment](https://pytorch.org/tutorials/recipes/torchscript_inference.html), [PyTorch C++ Examples](https://github.com/pytorch/examples/tree/main/cpp). For web apps, check out [Build a web application with ONNX Runtime](https://onnxruntime.ai/docs/tutorials/web/build-web-app.html). For examples of how to run PyTorch models with DirectML, see the [AI on Windows Sample Gallery](./samples/index.md).

- [**TensorFlow**](https://www.tensorflow.org/) is another popular open-source software library for machine learning and artificial intelligence used to build and deploy machine learning models for various tasks.

- [**WebNN API for web apps**](https://github.com/webmachinelearning/webnn/tree/main): A web standard for accessing neural network hardware acceleration in browsers, based on the WebIDL and JavaScript APIs. It enables web developers to create and run machine learning models efficiently on the client-side, without relying on cloud services or native libraries. [WebNN Samples on GitHub](https://github.com/webmachinelearning/webnn-samples). [WebNN samples using ONNX Runtime in the AI on Windows Sample Gallery](./samples/index.md#local-hardware-acceleration-through-directml).

## How do I fine-tune an ML model with my customized data to run on Windows?

[AI Toolkit for Visual Studio Code](./toolkit/index.md) is a VS Code extension that enables you to download and run AI models locally. The AI Tookit can also help you with:

- Testing models in an intuitive playground or in your application with a REST API.
- Fine-tuning your AI model, both locally or in the cloud (on a virtual machine) to create new skills, improve reliability of responses, set the tone and format of the response.
- Fine-tuning popular small-language models (SLMs), like [Phi-3](https://azure.microsoft.com/blog/introducing-phi-3-redefining-whats-possible-with-slms/) and [Mistral](https://mistral.ai/).
- Deploy your AI feature either to the cloud or with an application that runs on a device.

## How can I leverage hardware acceleration for better performance with AI features

DirectML is a low-level API that enables your Windows device hardware to accelerate the performance of ML models using the device GPU or NPU. Pairing DirectML with the ONNX Runtime is typically the most straightforward way for developers to bring hardware-accelerated AI to their users at scale. Learn more: [DirectML Overview](./directml/dml.md).
