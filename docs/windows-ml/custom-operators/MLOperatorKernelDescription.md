---
title: MLOperatorKernelDescription struct
description: Learn about the MLOperatorKernelDescription struct. This struct describes a custom operator kernel used to register that schema.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorKernelDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorKernelDescription
api_location:
- MLOperatorAuthor.h
---

# MLOperatorKernelDescription struct

Description of a custom operator kernel used to register that schema.

## Fields

| Name | Type | Description |
|------|------|-------------|
| defaultAttributeCount | **uint32_t** | The number of provided default attribute values. |
| defaultAttributes | **const** [MLOperatorAttributeNameValue](MLOperatorAttributeNameValue.md)* | The default values of attributes. These will be applied when the attributes are missing in a model containing the operator type. |
| domain | **const char*** | NULL-terminated UTF-8 string representing the name of the operator's domain. |
| executionOptions | **uint32_t** | Reserved for additional options. Must be 0. |
| executionType | [MLOperatorExecutionType](MLOperatorExecutionType.md) | Specifies whether a kernel uses the CPU or GPU for computation. |
| minimumOperatorSetVersion | **int32_t** | The minimum version of the operator sets for which this kernel is valid. The maximum version is inferred based on registrations of operator set schema for subsequent versions of the same domain. |
| name | **const char*** | NULL-terminated UTF-8 string representing the name of the operator. |
| options | [MLOperatorKernelOptions](MLOperatorKernelOptions.md) | Options for the kernel which apply to all execution provider types. |
| typeConstraintCount | **uint32_t** | The number of type constraints provided. |
| typeConstraints | **const** [MLOperatorEdgeTypeConstraint](MLOperatorEdgeTypeConstraint.md)* | An array of type constraints. Each constraint restricts input and outputs associated with a type label string to one or more edge types. |

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
