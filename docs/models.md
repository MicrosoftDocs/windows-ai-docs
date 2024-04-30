---
title: Get started using local Machine Learning models in your Windows app
description: TODO Description needed.
ms.author: mattwoj
author: mattwojo
ms.date: 05/21/2024
ms.topic: overview
#customer intent: As a <role>, I want <what> so that <why>.
---

# Get started using local Machine Learning models in your Windows app

Are you ready to start working directly with Machine Learning (ML) models, training or fine-tuning the model with your own custom data tailored to your needs, and running it locally in your app to preserve privacy, reduce latency, and save on cloud costs?

We can help you get started.

- [How can Windows applications leverage ML models?](#how-can-windows-applications-leverage-ml-models)

## How can Windows applications leverage ML models?

**Machine learning (ML)** is a branch of artificial intelligence that enables computers to learn from data and make predictions or decisions. **ML models** are algorithms that can be trained on data and then deployed to perform various tasks, such as content generation, reasoning over content, image recognition, natural language processing, sentiment analysis, and much more.

A few ways that Windows applications can leverage ML models to enhance their functionality and user experience, include:

- Apps can use Generative AI models to understand complex topics to summarize, rewrite, report on, or expand.
- Apps can use models that transform free-form content into a structured format that your app can understand.
- Apps can use Semantic Search models that allow users to search for content by meaning and quickly find related content.
- Apps can use natural language processing models to reason over complex natural language requirements, and plan and execute actions to accomplish the user's ask.
- Apps can use image manipulation models to intelligently modify images, erase or add subjects, upscale, or generate new content.
- Apps can use predictive diagnostic models to help identify and predict issues and help guide the user or do it for them.

Some of these scenarios can be enabled with [AI-backed APIs](apis.md) that abstract away the underlying ML models, whether those models are hosted on Azure in the cloud or run locally on the operating system in Windows.

However, working with ML models directly enables you to bring your own model, train or fine-tune the model with custom data to tailored to your needs, and run the model locally in your app to preserve privacy, reduce latency, and save on cloud costs.

## Where to find open source ML models on the web

Open Source ML models that are ready to use or customize are available in a variety of places, a few of the most popular include:

- [Hugging Face](https://huggingface.co/models): A hub of over 10,000 pre-trained ML models for natural language processing, powered by the Transformers library. You can find models for text classification, question answering, summarization, translation, generation, and more.
- [ONNX Model Zoo](https://onnx.ai/models/): A collection of pre-trained ML models in ONNX format that cover a wide range of domains and tasks, such as computer vision, natural language processing, speech, and more.
- [Qualcomm AI Hub](https://aihub.qualcomm.com/models): A platform that provides access to a variety of ML models and tools optimized for Qualcomm Snapdragon devices. You can find models for image, video, audio, and sensor processing, as well as frameworks, libraries, and SDKs for building and deploying ML applications on mobile devices. Qualcomm AI Hub also offers tutorials, guides, and community support for developers and researchers.
- [Pytorch Hub](https://pytorch.org/hub/): A pre-trained model repository designed to facilitate research reproducibility and enable new research. It is a simple API and workflow that provides the basic building blocks for improving machine learning research reproducibility. PyTorch Hub consists of a pre-trained model repository designed specifically to facilitate research reproducibility.
- [TensorFlow Hub](https://www.tensorflow.org/hub): A repository of pre-trained ML models and reusable components for TensorFlow, which is a popular framework for building and training ML models. You can find models for image, text, video, and audio processing, as well as transfer learning and fine-tuning.
- [Model Zoo](https://modelzoo.co/): A platform that curates and ranks the best open source ML models for various frameworks and tasks. You can browse models by category, framework, license, and rating, and see demos, code, and papers for each model.

Some model libraries are not intended to be customized and distributed via an app, but are helpful tools for hands-on exploration and discovery as a part of the development lifecycle, such as:

- [Ollama](https://ollama.com/library): Ollama is a marketplace of ready-to-use ML models for various tasks, such as face detection, sentiment analysis, or speech recognition. You can browse, test, and integrate the models into your app with a few clicks.
- [LM Studio](https://lmstudio.ai/): Lmstudio is a tool that lets you create custom ML models from your own data, using a drag-and-drop interface. You can choose from different ML algorithms, preprocess and visualize your data, and train and evaluate your models.

## ML Roles and Responsibilities

The process of creating and using ML models involves three main roles:

1. **Data Scientists**: Responsible for defining the problem, collecting and analyzing the data, choosing and training the ML algorithm, and evaluating and interpreting the results. They use tools such as Python, R, Jupyter Notebook, TensorFlow, PyTorch, and scikit-learn to perform these tasks.
2. **ML Engineers**: Responsible for deploying, monitoring, and maintaining the ML models in production environments. They use tools such as Docker, Kubernetes, Azure ML, AWS SageMaker, and Google Cloud AI Platform to ensure the scalability, reliability, and security of the ML models.
3. **App Developers**: Responsible for integrating the ML models into the app logic, UI, and UX. They use tools such as AI Fabric, ONNX Runtime, or REST APIs and process the user input and model output.

Each role involves different responsibilities and skills, but collaboration and communication between these roles is required to achieve the best results. Depending on the size and complexity of the project, these roles can be performed by the same person or by different teams.

## How to use ML models in Windows apps

There are different ways to use ML models in Windows apps, depending on the type, source, and format of the models, and the type of app.

A few ML model formats include:

- [**ONNX**](https://onnx.ai/) is a standardized model exchange format for representing and exchanging ML models across different frameworks and platforms.

- [**PyTorch**](https://pytorch.org/) is a popular open-source deep learning framework available with a Python and C++ interface.  

- [**TensorFlow**](https://www.tensorflow.org/) is another popular  open-source software library for machine learning and artificial intelligence used to build and deploy machine learning models for various tasks.

Using each model format:

- If you have a [pre-trained ML model in ONNX format](https://onnx.ai/models/), you can use the [ONNX Runtime (ORT)](https://onnxruntime.ai/) to load and run the model in your app. ORT allows you to access the hardware-accelerated inference capabilities of your device and optimize the performance of your ML model.

- If you have a pre-trained ML model in a different (non-ONNX) format, such as [PyTorch](https://pytorch.org/hub/) or [TensorFlow](https://www.tensorflow.org/hub), you can convert it to the standard ONNX exchangeable format so that the model can switch between frameworks and be compatible across platforms.

To convert a model to the exchangeable ONNX format, you can use tools like:

- [**Olive**](https://github.com/microsoft/Olive?tab=readme-ov-file#olive): A user-friendly, hardware-aware model optimization tool that can handle model compression, optimization, and compilation.
- [**Python Package Index (PyPI) WinMLTools**](https://pypi.org/project/winmltools/): A Python package that can convert ML models from various frameworks to ONNX.
- [**ONNX Runtime (ORT)**](https://onnxruntime.ai/): ONNX Runtime is a cross-platform library that can run ONNX models on different devices and platforms. You can use ONNX Runtime to convert ML models from TensorFlow or PyTorch to ONNX, or to run ONNX models directly in your app without using the Windows ML API.

A few resources to help you get started include:

- [**ONNX Get started guide**](https://onnx.ai/get-started.html): Build and train ML models, develop from scratch using the framework of your choice, use a Cloud service to help you build your model (no code or code-first), find supported tools, find tutorials for exporting to ONNX format, and learn more about ONNX concepts or operators in the documentation.
- [**TorchSharp**](https://github.com/dotnet/TorchSharp): 

## FAQs

Optional

## Related content

Optional

- [Related article title](link.md)
- [Related article title](link.md)
- [Related article title](link.md)
