---
author: alvinashcraft
title: AI model fine-tuning concepts
description: An overview of AI model fine tuning and when it can help get the most out of your data.
ms.author: aashcraft
ms.date: 05/09/2024
ms.topic: article
keywords: windows 11, windows ai, ai models, vs code, machine learning, fine-tuning
ai-usage: ai-assisted
---

# AI model fine-tuning concepts

Fine-tuning is a process of taking a pre-trained model and adjusting it to better fit your data. This process can help you get the most out of your data and improve the performance of your model. In this article, you'll learn the basic concepts of fine-tuning and when fine-tuning an AI model is appropriate.

## Introduction

Fine-tuning is a powerful technique that can help you get more out of your data. To understand fine-tuning, it's important to understand the concept of transfer learning. Transfer learning is a machine learning technique where a model trained on one task is re-purposed on a second related task. This is done by taking a pre-trained model and adjusting it to better fit the new data. Fine-tuning is a form of transfer learning where the pre-trained model is adjusted to better fit the new data.

There are several steps involved in fine-tuning a model. First, you need to select a pre-trained model that is well-suited to your task. Next, you need to prepare your sample data and fine-tune the model on this data. Finally, you need to iterate on your model to improve its performance.

## When to fine-tune

Fine-tuning is suited for times when you have a small amount of data and want to improve the performance of your model. By starting with a pre-trained model, you can leverage the knowledge that the model has already learned and adjust it to better fit your data. This can help you improve the performance of your model and reduce the amount of data needed to train it.

It's usually not necessary to fine-tune your model when you have a large amount of data. In this case, you can train your model from scratch and achieve good performance without fine-tuning. However, fine-tuning can still be useful in this case if you want to improve the performance of your model further. You may also want to fine-tune your model if you have a specific task that is different from the task the pre-trained model was originally trained on.

You may be able to avoid the costly fine-tuning of a model by using prompt engineering or prompt chaining. These techniques can help you generate high-quality text without the need for fine-tuning.

## Selecting a pre-trained model

You should select a pre-trained model that is well-suited to your task requirements. There are many pre-trained models available that have been trained on a wide range of tasks. You should choose a model that has been trained on a similar task to the one you are working on. This will help you leverage the knowledge that the model has already learned and adjust it to better fit your data.

[HuggingFace](https://huggingface.co/models) models are a good place to start when looking for pre-trained models. The HuggingFace models are grouped into categories based on the task they were trained on, making it easy to find a model that is well-suited to your task.

These categories include:

* Multimodal
* Computer vision
* Natural language processing
* Audio
* Tabular
* Reinforcement learning

Check the compatibility of the model with your environment and the tools you are using. For example, if you are using Visual Studio Code, you can use the [Azure Machine Learning extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) to fine-tune your model.

Check the status and license of the model. Some pre-trained models may be available under an open-source license, while others may require a commercial or personal license to use. All the models on HuggingFace include license information. Make sure you have the necessary permissions to use the model before fine-tuning it.

## Prepare your sample data

Preparing your sample data involves cleaning and preprocessing your data to make it suitable for training. You should also split your data into training and validation sets to evaluate the performance of your model. The format of your data should match the format expected by the pre-trained model you are using. This information can be found with the models on HuggingFace in the model card's **Instruction format** section. Most model cards will include a template for building a prompt for the model and some pseudo-code to help you get started.

## Iterating on your model

Once you have fine-tuned your model, you should evaluate its performance on the validation set. You can use metrics such as accuracy, precision, recall, and F1 score to evaluate the performance of your model. If the performance of your model is not satisfactory, you can iterate on your model by adjusting the hyperparameters, changing the architecture, or fine-tuning the model on more data. You can also examine the quality and diversity of your data to see if there are any issues that need to be addressed. As a general rule, a smaller set of high quality data is more valuable than a larger set of low quality data.

## See also

To learn more about fine-tuning AI models, check out the following resources:

* [Fine-tune a Llama 2 model in Azure AI Studio](/azure/ai-studio/how-to/fine-tune-model-llama)
* [Fine-tune a pretrained model on HuggingFace](https://huggingface.co/docs/transformers/training)
* [Fine-tuning a pre-trained model with TensorFlow](https://www.tensorflow.org/tutorials/images/transfer_learning)
