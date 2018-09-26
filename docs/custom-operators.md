---
author: eliotcowley
title: Custom operators
description: TODO
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators
ms.localizationpriority: medium
---

# Custom operators

The Windows Machine Learning custom operator Win32 APIs are located in **MLOperatorAuthor.h**.

## APIs

The following is a list of the custom operator APIs with their syntax and descriptions.

### MLOperatorAttributeType enum

Specifies the type of an attribute. Each attribute type numerically matches the corresponding ONNX type.

#### Fields

| Name        | Value | Description                            |
|-------------|-------|----------------------------------------|
| Undefined   | 0     | Undefined (unused).                    |
| Float       | 2     | 32-bit floating point.                 |
| Int         | 3     | 64-bit integer.                        |
| String      | 4     | String value.                          |
| FloatArray  | 7     | Array of 32-bit floating point values. |
| IntArray    | 8     | Array of 64-bit integer values.        |
| StringArray | 9     | Array of string values.                |

### MLOperatorTensorDataType enum

Specifies the data type of a tensor. Each data type numerically matches the corresponding ONNX type.

#### Fields

| Name       | Value | Description                             |
|------------|-------|-----------------------------------------|
| Undefined  | 0     | Undefined (unused).                     |
| Float      | 1     | IEEE 32-bit floating point.             |
| UInt8      | 2     | 8-bit unsigned integer.                 |
| Int8       | 3     | 8-bit signed integer.                   |
| UInt16     | 4     | 16-bit unsigned integer.                |
| Int16      | 5     | 16-bit signed integer.                  |
| Int32      | 6     | 32-bit signed integer.                  |
| Int64      | 7     | 64-bit signed integer.                  |
| String     | 8     | String (unsupported).                   |
| Bool       | 9     | 8-bit boolean. Values other than zero and one result in undefined behavior. |
| Float16    | 10    | IEEE 16-bit floating point.             |
| Double     | 11    | 64-bit double-precision floating point. |
| UInt32     | 12    | 32-bit unsigned integer.                |
| UInt64     | 13    | 64-bit unsigned integer.                |
| Complex64  | 14    | 64-bit complex type (unsupported).      |
| Complex128 | 15    | 128-bit complex type (unsupported).     |

### MLOperatorEdgeType enum

Specifies the types of an input or output edge of an operator.

#### Fields

| Name      | Value | Description |
|-----------|-------|-------------|
| Undefined | 0     |             |
| Tensor    | 1     |             |

### MLOperatorEdgeDescription struct

Specifies the properties of an input or output edge of an operator.

#### Fields

| Name           | Type                     | Description           |
|----------------|--------------------------|-----------------------|
| edgeType       | MLOperatorEdgeType       | The type of the edge. |
| reserved       | uint64_t                 |                       |
| tensorDataType | MLOperatorTensorDataType | The data type of a tensor. Used when **edgeType** is set to **Tensor**. |

### IMLOperatorAttributes interface

Represents the values of an operator's attributes, as determined by a model using the operator. This interface is called by implementations of custom operator kernels, and by implementations of shape and type inferrers.

#### Members

<table>
  <th>Syntax</th>
  <th>Description</th>
    <tr>
      <td>
        <pre>
          <code>
GetAttributeElementCount(
    _In_z_ const char* name,
    MLOperatorAttributeType type,
    _Out_ uint32_t* elementCount)
          </code>
        </pre>
      </td>
      <td>
        Gets the count of elements in an attribute. This may be used to determine if an attribute exists, and to determine the count of elements within an attribute of an array type.
      </td>
    </tr>
    <tr>
      <td>
        <pre>
          <code>
GetAttribute(
    _In_z_ const char* name,
    MLOperatorAttributeType type,
    uint32_t elementCount,
    size_t elementByteSize,
    _Out_writes_bytes_(elementCount * elementByteSize) void* value)
          </code>
        </pre>
      </td>
      <td>
        Gets the value of an attribute element which is of a numeric type. For attributes which are of array types, this method queries an individual element within the attribute at the specified index.
      </td>
    </tr>
    <tr>
      <td>
        <pre>
          <code>
GetStringAttributeElementLength(
    _In_z_ const char* name,
    uint32_t elementIndex,
    _Out_ uint32_t* attributeElementByteSize)
          </code>
        </pre>
      </td>
      <td>
        Gets the length of an attribute element which is of a string type. For attributes which are string arrays, this method queries the size of an individual element within the attribute at the specified index. The string is in UTF-8 format.  The size includes the null termination character.
      </td>
    </tr>
    <tr>
      <td>
        <pre>
          <code>
GetStringAttributeElement(
    _In_z_ const char* name,
    uint32_t elementIndex,
    uint32_t attributeElementByteSize,
    _Out_writes_(uint32_t) char* attributeElement)
          </code>
        </pre>
      </td>
      <td>
        Gets the value of an attribute element which is of a string type. For attributes which are string arrays, this method queries the value of an individual element within the attribute at the specified index. The string is in UTF-8 format.  The size includes the null termination character.
      </td>
    </tr>
</table>

[!INCLUDE [help](includes/get-help.md)]