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
"version": 3, 
  "actions": [ 
    { 
      "id": "Contoso.SampleGreeting", 
      "description": "Send greeting with Contoso", 
      "icon": "ms-resource//...", 
      "usesGenerativeAI": false,
      "isAvailable": false,
      "allowedAppInvokers": ["*"],
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

The **Version** field indicates the schema version in which the property was introduced. 

The **PWA** field indicates support for action providers that are implemented as a Progressive Web App (PWA). For more information on app actions with PWAs, see [Enable App Actions on Windows for a PWA](/microsoft-edge/progressive-web-apps/how-to/app-actions).

### Document root

| Property | Type | Description | Required | Version |
|----------|------|-------------|----------|---------|
| version | string | Schema version. When new functionality added, the version increments by one. | Yes. | 2 |
| actions | Action[] | Defines the actions provided by the app. | Yes. | 2 |

### Action

| Property | Type | Description | Required | Version | PWA |
|----------|------|-------------|----------|---------|-----|
| id | string | Action identifier. Must be unique per app package. This value is not localizable. | Yes | 2 | Yes |
| description | string | User-facing description for this action. This value is localizable. | Yes | 2 | Yes |
| icon | string | Localizable icon for the action. This value is an *ms-resource* string for an icon deployed with the app. | No | 2 | Yes |
| allowedAppInvokers | string[] | Specifies a list of Application User Model IDs (AppUserModelIDs) that can discover the action through a call to [GetActionsForInputs](/uwp/api/windows.ai.actions.hosting.actioncatalog.getactionsforinputs) or [GetAllActions](/uwp/api/windows.ai.actions.hosting.actioncatalog.getallactions). Wildcards are supported. "\*" will match all AppUserModelIDs. This is recommended for most actions, unless there is a specific reason to limit the callers that can invoke an action.  If **allowedAppInvokers** is omitted or is an empty list, no apps will be able to discover the action. For more information on AppUserModelIDs, see [Application User Model IDs](/windows/win32/shell/appids)| No | 3 | Yes |
| usesGenerativeAI | Boolean | Specifies whether the action uses generative AI. The default value is false. | No | 2 | Yes |
| isAvailable | Boolean | Specifies whether the action is available for use upon installation. The default value is true. | Yes | 2 | Yes |
| inputs | Inputs[] | List of entities that this action accepts as input. | Yes | 2 | Yes |
| inputCombinations | InputCombination[] | Provides descriptions for different combinations of inputs. | Yes | 2 | Yes |
| outputs | Output[] | If specified, must be an empty string in the current release. | No | 2 | Yes |
| invocation | Invocation | Provides information about how the action is invoked. | Yes | 2 | Yes |
| contentAgeRating | string | A field name from the [UserAgeConsentGroup](/uwp/api/windows.system.userageconsentgroup) that specifies the appropriate age rating for the action. The allowed values are "Child", "Minor", "Adult". If no value is specified, the default behavior allows access to all ages. | No | 2 | Yes |

### Output

| Property | Type | Description | Required | Version | PWA |
|----------|------|-------------|----------|---------|-----|
| name | string | The variable name of the entity. This value is not localizable. | Yes | 2 | Yes |
| kind | string | A field name from the **ActionEntityKind** enumeration specifying the entity type. This value is not localizable. The allowed values are "None", "Document", "File", "Photo", "Text", "StreamingText", "RemoteFile", "Table", "Contact". | Yes | 2 | Yes |

The schema version and PWA support vary for the different values of the **kind** property. See the entry for each entity type for details.

### InputCombination

| Property | Type | Description | Required | Version | PWA |
|----------|------|-------------|----------|---------|-----|
| inputs | string[] | A list of Input names for an action invocation. The list may be empty. | Yes | 2 | Yes |
| description | string | Description for the action invocation. This value is localizable. | No | 2 | Yes |
| where | string[] | One or more conditional statements determining the conditions under which the action applies. | No | 2 | Yes |

### Invocation

| Property | Type | Description | Required | Version | PWA |
|----------|------|-------------|----------|---------|-----|
| type | string | The instantiation type for the action. The allowed values are "uri" and "com". | Yes | 2 | Only "uri". |
| uri | string | The absolute URI for launching the action. Entity usage can be included within the string. A special reserved query string parameter, `token=${$.Token}` can be added to the URI to allow actions to validate that they have been invoked by the Action Runtime. For more information, see [Use the $.Token URI query string parameter](actions-filter-caller.md#use-the-token-uri-query-string-parameter). | Yes, for URI instantiated actions. | 2 | Yes |
| clsid | string | The class ID for the COM class that implements **IActionProvider**. | Yes, for COM actions. | 2 | No |
| inputData | JSON name/value pairs | A list of name/value pairs specifying additional data for URI actions. | No. Only valid for URI actions. | 2 | Yes |


## ActionEntityKind enumeration

The **ActionEntityKind** enumeration specifies the types of entities that are supported by App Actions on Windows. In the context of a JSON action definition, the entity kinds are string literals that are case-sensitive.

| Entity kind string | Description | Version | PWA |
|-------|------------|-------------|---------|-----|
| "File"  | Includes all file types that are not supported by photo or document entity types. | 2 | Input only. |
| "Photo" | Image file types. Supported image file extensions are ".jpg", ".jpeg", and ".png" | 2 | Input only. |
| "Document" | Document file types. Supported document file extensions are ".doc", ".docx", ".pdf", ".txt" | 2 | Input only. |
| "Text" | Supports strings of text. | 2 | Input only. |
| "StreamingText" | Supports incrementally streamed strings of text. | 2 | False |
| "RemoteFile" | Supports metadata to enable actions to validate and retrieve backing files from a cloud service. | 2 | Input only. |
| "Table" | A 2D table of string values that is serialized to a 1 dimensional array of strings. | 3 | No. |
| "Contact" | A set of data representing a contact. | 3 | Input only. |

## Entity properties

Each entity type supports one or more properties that provide instance data for the entity. Entity property names are case sensitive.

The following example illustrates how entities are referenced in the query string for actions that are launched via URI activation:

`...?param1=${entityName.property1}&param2=${entityName.property2}`

For information on using entity properties to create conditional sections in the action definition JSON, see [Where clauses](#where-clauses).

### File entity properties

| Property | Type | Description | Version | PWA |
|----------|------|-------------|---------|-----|
| "FileName" | string | The name of the file. | 2 | Input only. |
| "Path" | string | The path of the file. | 2 | Input only. |
| "Extension" | string | The extension of the file. | 2 | Input only. |

### Document entity properties

The *Document* entity supports the same properties as *File*.

### Photo entity properties

The *Photo* entity supports all of the properties of *File* in addition to the following properties.

| Property | Type | Description | Version | PWA |
|----------|------|-------------|---------|-----|
| "IsTemporaryPath" | Boolean | A value specifying whether the photo is stored in a temporary path. For example, this property is true for photos that are stored in memory from a bitmap, not stored permanently in a file. | 2 | Input only. |

### Text entity properties

| Property | Type | Description | Version | PWA |
|----------|------|-------------|---------|-----|
| "Text" | string | The full text. | 2 | Input only. |
| "ShortText" | string | A shortened version of the text, suitable for UI display. | 2 | Input only. |
| "Title" | string | The title of the text. | 2 | Input only. |
| "Description" | string | A description of the text. | 2 | Input only. |
| "Length" | double | The length of the text in characters. | 2 | Input only. |
| "WordCount" | double | The number of words in the text. | 2 | Input only. |

### StreamingText entity properties

| Property | Type | Description | Version | PWA |
|----------|------|-------------|---------|-----|
| "TextFormat" | string | The format of the streaming text. Supported values are "Plain", "Markdown". | 2 | No |

### RemoteFile entity properties

| Property | Type | Description | Version | PWA |
|----------|------|-------------|---------|-----|
| "AccountId" | string | The identifier of the cloud service account associated with the remote file. | 2 | Input only. |
| "ContentType" | string | The MIME type of the remote file. | 2 | Input only. |
| "DriveId" | string | The identifier for the remote drive associated with the remote file. | 2 | Input only. |
| "Extension" | string | The extension of the remote file. | 2 | Input only. |
| "FileId" | string | The identifier of the remote file. | 2 | Input only. |
| "FileKind" | **RemoteFileKind** | The remote file kind. | 2 | Input only. |
| "SourceId" | string | The identifier of the cloud service that hosts the remote file. | 2 | Input only. |
| "SourceUri" | string | The URI of the remote file. | 2 | Input only. |

## RemoteFileKind enumeration

The **RemoteFileKind** enumeration specifies the types of files that are supported for the **RemoteFile** entity.

| Entity kind string | Description | Version |
|-------|------------|-------------|---------|
| "File"  | Includes all file types that are not supported by photo or document entity types. | 2 |
| "Photo" | Image file types. Supported image file extensions are ".jpg", ".jpeg", and ".png" | 2 |
| "Document" | Document file types. Supported document file extensions are ".doc", ".docx", ".pdf", ".txt" | 2 |


### Table entity properties

| Property | Type | Description | Required | Version | PWA |
|----------|------|-------------|----------|---------|-----|
| "RowCount" | integer | The number of rows in the table. | Yes | 3 | No |
| "ColumnCount" | integer | The number of columns in the table. | Yes | 3 | No |
| "Title" | string | The title of the table. | No | 3 | No |
| "Description" | string | The description of the table.| No | 3 | No |

### Contact entity properties

| Property | Type | Description | Required | Version | PWA |
|----------|------|-------------|----------|---------|-----|
| "Email" | string | The email address of the contact. | No | 3 | Input only. |
| "FullName" | integer | The full name of the contact. | No | 3 | Input only. |
| "Title" | string | The title of the contact. | No | 3 | Input only. |
| "Description" | string | The description of the contact.| No | 3 | Input only. |

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


