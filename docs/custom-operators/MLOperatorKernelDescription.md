---
author: eliotcowley
title: MLOperatorKernelDescription struct
description: Description of a custom operator kernel used to register that schema.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorKernelDescription
ms.localizationpriority: medium
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

[!INCLUDE [help](../includes/get-help.md)]