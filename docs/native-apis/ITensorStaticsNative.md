---
author: eliotcowley
title: ITensorStaticsNative interface
description: Provides access to factory methods that enable the creation of ITensor objects using ID3D12Resource.
ms.author: elcowle
ms.date: 10/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, ITensorStaticsNative
ms.localizationpriority: medium
---

# ITensorStaticsNative interface

Provides access to factory methods that enable the creation of [ITensor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.itensor) objects using [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource).

## Methods

| Name | Description |
|------|-------------|
| [CreateFromD3D12Resource](ITensorStaticsNative_CreateFromD3D12Resource.md) | Creates a tensor object ([TensorFloat](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat), [TensorInt32Bit](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorint32bit)) from a user-specified [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource). |

[!INCLUDE [help](../includes/get-help.md)]