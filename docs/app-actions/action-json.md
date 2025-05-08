---
title: Action definition JSON schema for App Actions for Windows
description: Describes the format of the action definition JSON file format for Windows App Action providers.
ms.topic: article
ms.date: 02/04/2025
ms.localizationpriority: medium
---



# Action definition JSON schema for App Actions for Windows

This article describes the format of the action definition JSON file format for App Actions on Windows. This file must be included in your project with the **Build Action** set to "Content" and **Copy to Output Directory** set to “Copy if newer”. Specify the package-relative path to the JSON file in your package manifest XML file. For more information, see [Action provider package manifest XML format](action-provider-manifest.md).

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

The tables below describe the properties of the action definition JSON file.

### Document root

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| version | string | Schema version. When new functionality added, the version increments by one. | Yes. |
| actions | Action[] | Defines the actions provided by the app. | Yes. |

### Action

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| id | string | Action identifier. Must be unique per app package. This value is not localizable. | Yes |
| description | string | User-facing description for this action. This value is localizable. | Yes |
| icon | string | Localizable icon for the action. This value is an *ms-resource* string for an icon deployed with the app. | No |
| usesGenerativeAI | Boolean | Specifies whether the action uses generative AI. The default value is false. | No |
| isAvailable | Boolean | Specifies whether the action is available for use upon installation. The default value is true. | Yes |
| inputs | Inputs[] | List of entities that this action accepts as input. | Yes |
| inputCombinations | InputCombination[] | Provides descriptions for different combinations of inputs. | Yes |
| outputs | Output[] | If specified, must be an empty string in the current release. | No |
| invocation | Invocation | Provides information about how the action is invoked. | Yes |
| contentAgeRating | string | A field name from the [UserAgeConsentGroup](/uwp/api/windows.system.userageconsentgroup) that specifies the appropriate age rating for the action. The allowed values are "Child", "Minor", "Adult". If no value is specified, the default behavior allows access to all ages. | No |

### Output

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| name | string | The variable name of the entity. This value is not localizable. | Yes |
| kind | string | A field name from the **ActionEntityKind** enumeration specifying the entity type. This value is not localizable. The allowed values are "None", "Document", "File", "Photo", "Text". | Yes |

### InputCombination

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| inputs | string[] | A list of Input names for an action invocation. The list may be empty. | Yes |
| description | string | Description for the action invocation. This value is localizable. | No |
| where | string[] | One or more conditional statements determining the conditions under which the action applies. | No |

### Invocation

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| type | string | The instantiation type for the action. The allowed values are "uri" and "com" | Yes |
| uri | string | The absolute URI for launching the action. Entity usage can be included within the string. | Yes, for URI instantiated actions. |
| clsid | string | The class ID for the COM class that implements **IActionProvider**. | Yes, for COM actions |
| inputData | A list of name/value pairs specifying additional data for URI actions. | No. Only valid for URI actions. |


## ActionEntityKind enumeration

The **ActionEntityKind** enumeration specifies the types of entities that are supported by App Actions for Windows. In the context of a JSON action definition, the entity kinds are string literals that are case-sensitive.

| Entity kind string | Description |
|-------|------------|-------------|
| "File"  | Includes all file types that are not supported by photo or document entity types. |
| "Photo" | Image file types. Supported image file extensions are ".jpg", ".jpeg", and ".png" |
| "Document" | Document file types. Supported document file extensions are ".doc", ".docx", ".pdf", ".txt" |
| "Text" | Supports strings of text. |
| "StreamingText" | Supports incrementally streamed strings of text. |
| "RemoteFile" | Supports metadata to enable actions to validate and retrieve backing files from a cloud service. |

## Entity properties

Each entity type supports one or more properties that provide instance data for the entity. Entity property names are case sensitive.

The following example illustrates how entities are referenced in the query string for actions that are launched via URI activation:

`...?param1=${entityName.property1}&param2=${entityName.property2}`

For information on using entity properties to create conditional sections in the action definition JSON, see [Where clauses](#where-clauses).

### File entity properties

| Property | Type | Description |
|----------|------|-------------|
| "FileName" | string | The name of the file. |
| "Path" | string | The path of the file. |
| "Extension" | string | The extension of the file. |

### Document entity properties

The *Document* entity supports the same properties as *File*.

### Photo entity properties

The *Photo* entity supports all of the properties of *File* in addition to the following properties.

| Property | Type | Description |
|----------|------|-------------|
| "IsTemporaryPath" | Boolean | A value specifying whether the photo is stored in a temporary path. For example, this property is true for photos that are stored in memory from a bitmap, not stored permanently in a file. |

### Text entity properties

| Property | Type | Description |
|----------|------|-------------|
| "Text" | string | The full text. |
| "ShortText" | string | A shortened version of the text, suitable for UI display. |
| "Title" | string | The title of the text. |
| "Description" | string | A description of the text. |
| "Length" | double | The length of the text in characters. |
| "WordCount" | double | The number of words in the text. | 

### StreamingText entity properties

| Property | Type | Description |
|----------|------|-------------|
| "TextFormat" | string | TBD |

### RemoteFile entity properties

| Property | Type | Description |
|----------|------|-------------|
| "AccountId" | string | The identifier of the cloud service account associated with the remote file. |
| "ContentType" | string | The MIME type of the remote file. |
| "DriveId" | string | TBD |
| "Extension" | string | The extension of the remote file. |
| "FileId" | string | The identifier of the remote file. |
| "FileKind" | **RemoteFileKind** | The remote file kind. |
| "SourceId" | string | The identifier of the cloud service that hosts the remote file. |
| "SourceUri" | string | The URI of the remote file. |

## RemoteFileKind enumeration

The **RemoteFileKind** enumeration specifies the types of files that are supported for the **RemoteFile** entity.

| Entity kind string | Description |
|-------|------------|-------------|
| "File"  | Includes all file types that are not supported by photo or document entity types. |
| "Photo" | Image file types. Supported image file extensions are ".jpg", ".jpeg", and ".png" |
| "Document" | Document file types. Supported document file extensions are ".doc", ".docx", ".pdf", ".txt" |

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


