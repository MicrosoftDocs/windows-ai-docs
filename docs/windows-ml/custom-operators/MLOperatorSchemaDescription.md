---
title: MLOperatorSchemaDescription struct
description: Description of a custom operator schema used to register that schema.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorSchemaDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorSchemaDescription
api_location:
- MLOperatorAuthor.h
---

# MLOperatorSchemaDescription struct

Description of a custom operator schema used to register that schema.

## Fields

| Name | Type | Description |
|------|------|-------------|
| attributeCount | **uint32_t** | The number of provided attributes. |
| attributes | **const** [MLOperatorAttribute](MLOperatorAttribute.md)* | The set of attributes supported by the operator type. |
| defaultAttributeCount | **uint32_t** | The number of provided default attribute values. |
| defaultAttributes | **const** [MLOperatorAttributeNameValue](MLOperatorAttributeNameValue.md)* | The default values of attributes. These will be applied when the attributes are missing in a model containing the operator type. |
| inputCount | **uint32_t** | The number of inputs of the operator. |
| inputs | **const** [MLOperatorSchemaEdgeDescription](MLOperatorSchemaEdgeDescription.md)* | An array containing the descriptions of the operator's input edges. |
| name | **const char*** | NULL-terminated UTF-8 string representing the name of the operator. |
| operatorSetVersionAtLastChange | **int32_t** | The operator set version at which this operator was introduced or last changed. |
| outputCount | **uint32_t** | The number of outputs of the operator. |
| outputs | **const** [MLOperatorSchemaEdgeDescription](MLOperatorSchemaEdgeDescription.md)* | An array containing the descriptions of the operator's output edges. |
| typeConstraintCount | **uint32_t** | The number of type constraints provided. |
| typeConstraints | **const** [MLOperatorEdgeTypeConstraint](MLOperatorEdgeTypeConstraint.md)* | An array of type constraints. Each constraint restricts input and outputs associated with a type label string to one or more edge types. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
