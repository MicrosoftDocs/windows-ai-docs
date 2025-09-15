---
author: alvinashcraft
title: AI model fine-tuning concepts
description: Learn AI model fine-tuning concepts and discover when fine-tuning can optimize your data and improve model performance. Get started with practical guidance.
ms.author: aashcraft
ms.date: 09/11/2025
ms.topic: concept-article
ai-usage: ai-assisted
no-loc: [Llama, HuggingFace, TensorFlow, Visual Studio Code, Azure Machine Learning, VS Code]
# customer intent: As a Windows developer, I want to understand the concepts of AI model fine-tuning so that I can determine when it is appropriate for my data and use case.
---

# Model fine-tuning concepts

Fine-tuning helps you adapt pre-trained AI models to work better with your specific data and use cases. This technique can improve model performance while requiring less training data than building a model from scratch.

This article covers:

- What fine-tuning is and how it works
- When to use fine-tuning versus other approaches
- How to select and prepare models for fine-tuning
- Best practices for iterating and improving your results

## Prerequisites

Before you start, you should have:

- Basic understanding of machine learning concepts
- Familiarity with your specific use case and data requirements
- Access to sample data for training and validation

## What is fine-tuning?

Fine-tuning is a machine learning technique that adapts a pre-trained model to perform better on your specific task. Instead of training a model from scratch, you start with a model that already understands general patterns and adjust it to work with your data.

This approach leverages transfer learning—using knowledge gained from one task to improve performance on a related task. Fine-tuning is particularly effective when you have limited training data or want to build on existing model capabilities.

## When to fine-tune

Fine-tuning is suited for times when you have a small amount of data and want to improve the performance of your model. By starting with a pre-trained model, you can leverage the knowledge that the model has already learned and adjust it to better fit your data. This can help you improve the performance of your model and reduce the amount of data needed to train it.

It's usually not necessary to fine-tune your model when you have a large amount of data. In this case, you can train your model from scratch and achieve good performance without fine-tuning. However, fine-tuning can still be useful in this case if you want to improve the performance of your model further. You may also want to fine-tune your model if you have a specific task that is different from the task the pre-trained model was originally trained on.

You may be able to avoid the costly fine-tuning of a model by using prompt engineering or prompt chaining. These techniques can help you generate high-quality text without the need for fine-tuning.

## Select a pre-trained model

You should select a pre-trained model that is well-suited to your task requirements. There are many pre-trained models available that have been trained on a wide range of tasks. You should choose a model that has been trained on a similar task to the one you are working on. This will help you leverage the knowledge that the model has already learned and adjust it to better fit your data.

[HuggingFace](https://huggingface.co/models) models are a good place to start when looking for pre-trained models. The HuggingFace models are grouped into categories based on the task they were trained on, making it easy to find a model that is well-suited to your task.

These categories include:

- Multimodal
- Computer vision
- Natural language processing
- Audio
- Tabular
- Reinforcement learning

Check the compatibility of the model with your environment and the tools you are using. For example, if you are using Visual Studio Code (VS Code), you can use the [Azure Machine Learning extension for VS Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) to fine-tune your model.

Check the status and license of the model. Some pre-trained models may be available under an open-source license, while others may require a commercial or personal license to use. All the models on HuggingFace include license information. Make sure you have the necessary permissions to use the model before fine-tuning it.

## Prepare your sample data

Preparing your sample data involves cleaning and preprocessing your data to make it suitable for training. You should also split your data into training and validation sets to evaluate the performance of your model. The format of your data should match the format expected by the pre-trained model you are using. This information can be found with the models on HuggingFace in the **Instruction format** section of the model card. Most model cards will include a template for building a prompt for the model and some pseudo-code to help you get started.

## Iterate on your model

Once you have fine-tuned your model, you should evaluate its performance on the validation set. You can use metrics such as accuracy, precision, recall, and F1 score to evaluate the performance of your model. If the performance of your model is not satisfactory, you can iterate on your model by adjusting the hyperparameters, changing the architecture, or fine-tuning the model on more data. You can also examine the quality and diversity of your data to see if there are any issues that need to be addressed. As a general rule, a smaller set of high quality data is more valuable than a larger set of low quality data.

## See also

To learn more about fine-tuning AI models, check out the following resources:

- [Fine-tune a Llama 2 model in Azure AI Foundry portal](/azure/ai-studio/how-to/fine-tune-model-llama)
- [Fine-tune a pretrained model on HuggingFace](https://huggingface.co/docs/transformers/training)
- [Fine-tuning a pre-trained model with TensorFlow](https://www.tensorflow.org/tutorials/images/transfer_learning)

When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](rai.md).

## Related content

- [What is the AI Toolkit for VS Code?](toolkit/index.md)
- [What is Windows AI Foundry?](overview.md)
- [Frequently Asked Questions about using AI in Windows apps](faq.yml)
