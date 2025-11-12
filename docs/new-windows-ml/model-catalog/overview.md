---
title: Download and share models on-device with the Windows ML Model Catalog APIs
description: Learn about Windows ML Model Catalog APIs that allow your app or library to dynamically download large AI model files to a shared on-device location from your own online model catalogs.
ms.topic: overview
ms.date: 09/26/2024
---

# Download and share models on-device with the Windows ML Model Catalog APIs

The Windows ML Model Catalog APIs allow your app or library to dynamically download large AI model files to a shared on-device location from your own online model catalogs without shipping those large files directly with your app or library. Additionally, the model catalog will help filter which models are compatible with the Windows device it's running on, so that the right model is downloaded to the device.

## What are the Model Catalog APIs?

The Model Catalog APIs are a set of APIs that can be connected to one or many cloud model catalogs to facilitate downloading and storing those models locally on the device so that they can be used by any Windows applications on the device. The APIs have a few core features:

- **Add catalogs**: Add one or many online catalogs
- **Discover compatible models**: Automatically find models that work with the user's hardware and execution providers
- **Download models**: Download and store models from various sources
- **Share models across apps**: If multiple applications are requesting the same model (same SHA256 hash), the model will be shared on disk without duplicating downloads

## Key features

### Automatic compatibility matching

Model Catalog automatically matches models to your system's available execution providers (CPU, GPU, NPU, etc.). When you request a model, the catalog only returns models that are compatible with your current hardware configuration.

### Model storage

Downloaded models are stored in a user-specific location. If multiple applications request the same model (same SHA256 hash), the already downloaded model will be shared among those applications.

### Multiple catalog sources

Your application can configure multiple catalog sources, allowing you to:
- Use models from multiple vendors or repositories
- Prioritize certain sources over others
- Include your own private model catalogs alongside public ones

## How it works

The Model Catalog system consists of several components:

1. **Catalog sources**: Define where models can be found (URLs to catalog JSON files)
2. **Model matching**: Filters available models based on execution provider compatibility
3. **Download management**: Handles download and storage of model files
4. **Instance management**: Provides access to downloaded models while your app is running

## Model identification

Models in the catalog have two types of identifiers:

- **Name**: A common name like "gpt2" (multiple model variations can share the same name)
- **Id**: A unique-in-the-catalog identifier that typically includes execution provider information, like "gpt2-cpu" or "gpt2-npu"

Applications typically use `FindModelAsync` with the Name for simplicity, letting the catalog select the best available model variant for the current system based on execution provider compatibility.

## Execution provider support

Model Catalog supports a variety of execution providers. See the [supported execution providers in Windows ML docs](../supported-execution-providers.md) for more info.

## Catalog Source schema

Model catalog sources use a standardized JSON schema that defines:
- Model metadata (name, id, version, publisher)
- Supported execution providers
- Download URLs and file information
- License information
- Model size details

For detailed schema information, see [Model Catalog Source](./model-catalog-source.md).

## Getting started

To start using Model Catalog in your Windows ML application:

1. Configure your catalog sources
2. Create a `ModelCatalog` instance
3. Query and download models
4. Inference your models with your desired runtime!

For a complete walkthrough, see [Get started with Model Catalog](./get-started.md).

## Next steps

- [Get started with Model Catalog](./get-started.md)
- [Model Catalog Source schema reference](./model-catalog-source.md)
- [Windows ML overview](../overview.md)