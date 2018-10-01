---
author: eliotcowley
title: MLOperatorSchemaDescription struct
description: Description of a custom operator schema used to register that schema.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorSchemaDescription
ms.localizationpriority: medium
---

# MLOperatorSchemaDescription struct

Description of a custom operator schema used to register that schema.

## Fields

| Name | Type | Description |
|------|------|-------------|
| name | const char* | NULL-terminated UTF-8 string representing the name of the operator. |
| operatorSetVersionAtLastChange | int32_t | The operator set version at which this operator was introduced or last changed. |
| inputs | const MLOperatorSchemaEdgeDescription* | An array containing the descriptions of the operator's input edges. |
| inputCount | uint32_t | The number of inputs of the operator. |
| outputs | const MLOperatorSchemaEdgeDescription* | An array containing the descriptions of the operator's output edges. |
| outputCount | uint32_t | The number of outputs of the operator. |
| typeConstraints | const MLOperatorEdgeTypeConstraint* | An array of type constraints. Each constraint restricts input and outputs associated with a type label string to one or more edge types. |
| typeConstraintCount | uint32_t | The number of type constraints provided. |
| attributes | const MLOperatorAttribute* | The set of attributes supported by the operator type. |
| attributeCount | uint32_t | The number of provided attributes. |
| defaultAttributes | const MLOperatorAttributeNameValue* | The default values of attributes. These will be applied when the attributes are missing in a model containing the operator type. |
| defaultAttributeCount | uint32_t | The number of provided default attribute values. |

[!INCLUDE [help](../includes/get-help.md)]