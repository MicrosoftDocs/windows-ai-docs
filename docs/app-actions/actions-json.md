---
title: Action definition JSON schema for App Actions on Windows
description: Describes the format of the action definition JSON file format for Windows App Action providers.
ms.topic: how-to
ms.date: 02/04/2025
ms.localizationpriority: medium
---



# Action definition JSON schema for App Actions on Windows

This article describes the format of the action definition JSON file format for App Actions on Windows. This file must be included in your project with the **Build Action** set to "Content" and **Copy to Output Directory** set to “Copy if newer”. Specify the package-relative path to the JSON file in your package manifest XML file. For more information, see [Action provider package manifest XML format](actions-provider-manifest.md).

## Example action definition JSON file

```json
"version": 2, 
  "actions": [ 
    { 
      "id": "Contoso.SampleGreeting", 
      "description": "Send greeting with Contoso", 
      "icon": "ms-resource//...", 
      "usesGenerativeAI": false,
      "isAvailable": false,
      "inputs": [ 
        { 
          "name": "UserFriendlyName", 
          "kind": "Text" 
        }, 
        { 
          "name": "PetName", 
          "kind": "Text", 
          "required": false 
        } 
      ], 
      "inputCombinations": [ 
        { 
          "inputs": ["UserFriendlyName"], 
          "description": "Greet ${UserFriendlyName.Text}" 
        }, 
        { 
          "inputs": ["UserFriendlyName", "PetName"], 
          "description": "Greet ${UserFriendlyName.Text} and their pet ${PetName.Text}" 
        } 
      ], 
      "contentAgeRating": "child",  
      "invocation": 
      {
        { 
          "type": "Uri", 
          "uri": "contoso://greetUser?userName=${UserFriendlyName.Text}&petName=${PetName.Text}", 
        }, 
        "where": [ 
          "${UserFriendlyName.Length > 3}" 
        ] 
      } 
    }, 
    { 
      "id": "Contoso.SampleGetText", 
      "description": "Summarize file with Contoso", 
      "icon": "ms-resource://...", 
      "inputs": [ 
        { 
          "name": "FileToSummarize", 
          "kind": "File" 
        } 
      ], 
      "inputCombinations": [ 
        { 
          "inputs": ["FileToSummarize"], 
          "description": "Summarize ${FileToSummarize.Path}" 
        }, 
      ], 
      "outputs": [ 
        { 
          "name": "Summary", 
          "kind": "Text" 
        } 
      ],
      "contentAgeRating": "child", 
      "invocation": { 
        "type": "COM", 
        "clsid": "{...}" 
      } 
    } 
  ] 
} 
```

## Action definition JSON properties

The tables below describe the properties of the action definition JSON file. The **Version** field indicates the schema version in which the property was introduced.

### Document root

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| version | string | Schema version. When new functionality added, the version increments by one. | Yes. | 2 |
| actions | Action[] | Defines the actions provided by the app. | Yes. | 2 |

### Action

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| id | string | Action identifier. Must be unique per app package. This value is not localizable. | Yes | 2 |
| description | string | User-facing description for this action. This value is localizable. | Yes | 2 |
| icon | string | Localizable icon for the action. This value is an *ms-resource* string for an icon deployed with the app. | No | 2 |
| usesGenerativeAI | Boolean | Specifies whether the action uses generative AI. The default value is false. | No | 2 |
| isAvailable | Boolean | Specifies whether the action is available for use upon installation. The default value is true. | Yes | 2 }
| inputs | Inputs[] | List of entities that this action accepts as input. | Yes | 2 |
| inputCombinations | InputCombination[] | Provides descriptions for different combinations of inputs. | Yes | 2 |
| outputs | Output[] | If specified, must be an empty string in the current release. | No | 2 |
| invocation | Invocation | Provides information about how the action is invoked. | Yes | 2 |
| contentAgeRating | string | A field name from the [UserAgeConsentGroup](/uwp/api/windows.system.userageconsentgroup) that specifies the appropriate age rating for the action. The allowed values are "Child", "Minor", "Adult". If no value is specified, the default behavior allows access to all ages. | No | 2 |

### Output

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| name | string | The variable name of the entity. This value is not localizable. | Yes | 2 |
| kind | string | A field name from the **ActionEntityKind** enumeration specifying the entity type. This value is not localizable. The allowed values are "None", "Document", "File", "Photo", "Text". | Yes | 2 |

### InputCombination

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| inputs | string[] | A list of Input names for an action invocation. The list may be empty. | Yes | 2 |
| description | string | Description for the action invocation. This value is localizable. | No | 2 |
| where | string[] | One or more conditional statements determining the conditions under which the action applies. | No | 2 |

### Invocation

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| type | string | The instantiation type for the action. The allowed values are "uri" and "com" | Yes | 2 |
| uri | string | The absolute URI for launching the action. Entity usage can be included within the string. | Yes, for URI instantiated actions. | 2 |
| clsid | string | The class ID for the COM class that implements **IActionProvider**. | Yes, for COM actions | 2 |
| inputData | A list of name/value pairs specifying additional data for URI actions. | No. Only valid for URI actions. | 2 |


## ActionEntityKind enumeration

The **ActionEntityKind** enumeration specifies the types of entities that are supported by App Actions on Windows. In the context of a JSON action definition, the entity kinds are string literals that are case-sensitive.

| Entity kind string | Description | Version |
|-------|------------|-------------|---------|
| "File"  | Includes all file types that are not supported by photo or document entity types. | 2 |
| "Photo" | Image file types. Supported image file extensions are ".jpg", ".jpeg", and ".png" | 2 |
| "Document" | Document file types. Supported document file extensions are ".doc", ".docx", ".pdf", ".txt" | 2 |
| "Text" | Supports strings of text. | 2 |
| "StreamingText" | Supports incrementally streamed strings of text. | 2 |
| "RemoteFile" | Supports metadata to enable actions to validate and retrieve backing files from a cloud service. | 2 |
| "Table" | A 2D table of string values that is serialized to a 1 dimensional array of strings. | 3 |
| "Contact" | A set of data representing a contact. | 3 |

## Entity properties

Each entity type supports one or more properties that provide instance data for the entity. Entity property names are case sensitive.

The following example illustrates how entities are referenced in the query string for actions that are launched via URI activation:

`...?param1=${entityName.property1}&param2=${entityName.property2}`

For information on using entity properties to create conditional sections in the action definition JSON, see [Where clauses](#where-clauses).

### File entity properties

| Property | Type | Description | Version |
|----------|------|-------------|---------|
| "FileName" | string | The name of the file. | 2 |
| "Path" | string | The path of the file. | 2 |
| "Extension" | string | The extension of the file. | 2 |

### Document entity properties

The *Document* entity supports the same properties as *File*.

### Photo entity properties

The *Photo* entity supports all of the properties of *File* in addition to the following properties.

| Property | Type | Description | Version |
|----------|------|-------------|---------|
| "IsTemporaryPath" | Boolean | A value specifying whether the photo is stored in a temporary path. For example, this property is true for photos that are stored in memory from a bitmap, not stored permanently in a file. | 2 |

### Text entity properties

| Property | Type | Description | Version |
|----------|------|-------------|---------|
| "Text" | string | The full text. | 2 |
| "ShortText" | string | A shortened version of the text, suitable for UI display. | 2 |
| "Title" | string | The title of the text. | 2 |
| "Description" | string | A description of the text. | 2 |
| "Length" | double | The length of the text in characters. | 2 |
| "WordCount" | double | The number of words in the text. | 2 |

### StreamingText entity properties

| Property | Type | Description | Version |
|----------|------|-------------|---------|
| "TextFormat" | string | The format of the streaming text. Supported values are "Plain", "Markdown". | 2 |

### RemoteFile entity properties

| Property | Type | Description | Version |
|----------|------|-------------|---------|
| "AccountId" | string | The identifier of the cloud service account associated with the remote file. | 2 |
| "ContentType" | string | The MIME type of the remote file. | 2 |
| "DriveId" | string | The identifier for the remote drive associated with the remote file. | 2 |
| "Extension" | string | The extension of the remote file. | 2 |
| "FileId" | string | The identifier of the remote file. | 2 |
| "FileKind" | **RemoteFileKind** | The remote file kind. | 2 |
| "SourceId" | string | The identifier of the cloud service that hosts the remote file. | 2 |
| "SourceUri" | string | The URI of the remote file. | 2 |

## RemoteFileKind enumeration

The **RemoteFileKind** enumeration specifies the types of files that are supported for the **RemoteFile** entity.

| Entity kind string | Description | Version |
|-------|------------|-------------|---------|
| "File"  | Includes all file types that are not supported by photo or document entity types. | 2 |
| "Photo" | Image file types. Supported image file extensions are ".jpg", ".jpeg", and ".png" | 2 |
| "Document" | Document file types. Supported document file extensions are ".doc", ".docx", ".pdf", ".txt" | 2 |


### Table entity properties

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| "RowCount" | integer | The number of rows in the table. | Yes | 3 |
| "ColumnCount" | integer | The number of columns in the table. | Yes | 3 |
| "Title" | string | The title of the table. | No | 3 |
| "Description" | string | The description of the table.| No | 3 |

### Contact entity properties

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| "Email" | string | The email address of the contact. | No | 3 |
| "FullName" | integer | The full name of the contact. | No | 3 |
| "Title" | string | The title of the contact. | No | 3 |
| "Description" | string | The description of the contact.| No | 3 |

## Where clauses

The action definition JSON format supports *where* clauses that can be used to implement conditional logic, such as specifying that an action should be invoked only when an entity property has a specified value.

The following operators can be used with *where* clauses.

| Operator | Description |
|----------|-------------|
| == | Equality |
| ~= | Case-insensitive equality |
| != | Inequality |
| < | Less than |
| <= | Less than or equal to |
| > | Greater than |
| >= | Greater than or equal to |
| \|\| | Logical OR |
| && | Logical AND |

*Where* clauses use the following format:

```json
"where": [ 
    "${<property_accessor>} <operator> <value>" 
] 
```

The following example shows a *where* clause that evaluates to true if a **File** entity has the file extension ".txt".

```json
"where": [ 
    "${File.Extension} ~= \".txt\"" 
] 
```

Multiple *where* clauses can be combined using the logical AND and logical OR operators.

```json
"where": [ 
  "${File.Extension} ~= \".txt\" || ${File.Extension} ~= \".md\"" 
] 
```

## Related articles


