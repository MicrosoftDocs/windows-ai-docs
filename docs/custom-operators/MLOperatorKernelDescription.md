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
| domain | const char* | NULL-terminated UTF-8 string representing the name of the operator's domain. |
| name | const char* | NULL-terminated UTF-8 string representing the name of the operator. |
| minimumOperatorSetVersion | int32_t | The minimum version of the operator sets for which this kernel is valid. The maximum version is inferred based on registrations of operator set schema for subsequent versions of the same domain. |
| executionType | MLOperatorExecutionType | Specifies whether a kernel uses the CPU or GPU for computation. |
| typeConstraints | const MLOperatorEdgeTypeConstraint* | An array of type constraints. Each constraint restricts input and outputs associated with a type label string to one or more edge types. |
| typeConstraintCount | uint32_t | The number of type constraints provided. |
| defaultAttributes | const MLOperatorAttributeNameValue* | The default values of attributes. These will be applied when the attributes are missing in a model containing the operator type. |
| defaultAttributeCount | uint32_t | The number of provided default attribute values. |
| options | MLOperatorKernelOptions | Options for the kernel which apply to all execution provider types. |
| executionOptions | uint32_t | Reserved for additional options. Must be 0. |

[!INCLUDE [help](../includes/get-help.md)]