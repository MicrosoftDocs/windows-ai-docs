---
title: API ref for AI-backed content safety moderation in the Windows App SDK
description: Learn about the Windows App SDK APIs, backed by artificial intelligence (AI), that can moderate content through sensitivity filters.
ms.topic: article
ms.date: 04/14/2025
---

# API ref for Content Safety Moderation in the Windows App SDK

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

Learn about the [Windows App SDK](/windows/apps/windows-app-sdk/), backed by artificial intelligence (AI), that can moderate content through sensitivity filters.

For more details, see [Content Safety Moderation with Windows Copilot Runtime](content-moderation.md).

> [!TIP]
> Provide feedback on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo (include **Content Moderation** in the title) or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

---

<!---
-api-id: N:Microsoft.Windows.AI.ContentModeration
-api-type: winrt namespace
--->

## Microsoft.Windows.AI.ContentModeration

Provides APIs for machine learning models that perform content moderation.



<!---
-api-id: T:Microsoft.Windows.AI.ContentModeration.ContentFilterOptions
-api-type: winrt class
--->

### ContentFilterOptions class

```
public sealed class ContentFilterOptions
```


<!---
-api-id: M:Microsoft.Windows.AI.ContentModeration.ContentFilterOptions.#ctor
-api-type: winrt constructor
--->

#### ContentFilterOptions.#ctor constructor

```
public ContentFilterOptions ();
```



<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.ContentFilterOptions.ImageMaxAllowedSeverityLevel
-api-type: winrt property
--->

#### ContentFilterOptions.ImageMaxAllowedSeverityLevel property

```
public Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity ImageMaxAllowedSeverityLevel { get; set; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.ContentFilterOptions.PromptMaxAllowedSeverityLevel
-api-type: winrt property
--->

#### ContentFilterOptions.PromptMaxAllowedSeverityLevel property

```
public Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity PromptMaxAllowedSeverityLevel { get; set; }
```

##### Property value



<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.ContentFilterOptions.ResponseMaxAllowedSeverityLevel
-api-type: winrt property
--->

#### ContentFilterOptions.ResponseMaxAllowedSeverityLevel property

```
public Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity ResponseMaxAllowedSeverityLevel { get; set; }
```

##### Property value





<!---
-api-id: T:Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity
-api-type: winrt class
--->

### ImageContentFilterSeverity class

```
public sealed class ImageContentFilterSeverity
```


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity.AdultContentLevel
-api-type: winrt property
--->

#### ImageContentFilterSeverity.AdultContentLevel property

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel AdultContentLevel { get; set; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity.GoryContentLevel
-api-type: winrt property
--->

#### ImageContentFilterSeverity.GoryContentLevel property

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel GoryContentLevel { get; set; }
```

##### Property value



<!---
-api-id: M:Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity.#ctor
-api-type: winrt constructor
--->

#### ImageContentFilterSeverity.#ctor constructor

```
public ImageContentFilterSeverity ();
```


<!---
-api-id: M:Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity.#ctor
-api-type: winrt constructor
--->

#### ImageContentFilterSeverity.#ctor constructor

```
public ImageContentFilterSeverity ();
```


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity.RacyContentLevel
-api-type: winrt property
--->

#### ImageContentFilterSeverity.RacyContentLevel property

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel RacyContentLevel { get; set; }
```

##### Property value



<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.ImageContentFilterSeverity.ViolentContentLevel
-api-type: winrt property
--->

#### ImageContentFilterSeverity.ViolentContentLevel

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel ViolentContentLevel { get; set; }
```

##### Property value




<!---
-api-id: T:Microsoft.Windows.AI.ContentModeration.SeverityLevel
-api-type: winrt enum
--->

### SeverityLevel enumeration

```
public enum SeverityLevel
```

#### Fields

##### Minimum: 10

##### Low: 11

##### Medium: 12

##### High: 13




<!---
-api-id: T:Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity
-api-type: winrt class
--->

### TextContentFilterSeverity class

```
public sealed class TextContentFilterSeverity
```


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity.Hate
-api-type: winrt property
--->

#### TextContentFilterSeverity.Hate property

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel Hate { get; set; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity.SelfHarm
-api-type: winrt property
--->

#### TextContentFilterSeverity.SelfHarm property

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel SelfHarm { get; set; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity.Sexual
-api-type: winrt property
--->

#### TextContentFilterSeverity.Sexual property

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel Sexual { get; set; }
```

##### Property value



<!---
-api-id: M:Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity.#ctor
-api-type: winrt constructor
--->

#### TextContentFilterSeverity.#ctor constructor

```
public TextContentFilterSeverity ();
```



<!---
-api-id: M:Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity.#ctor(Microsoft.Windows.AI.ContentModeration.SeverityLevel)
-api-type: winrt constructor
--->

#### TextContentFilterSeverity.#ctor(Microsoft.Windows.AI.ContentModeration.SeverityLevel) constructor

```
public TextContentFilterSeverity (Microsoft.Windows.AI.ContentModeration.SeverityLevel severityForAllCategories);
```

##### Parameters

###### severityForAllCategories


<!---
-api-id: P:Microsoft.Windows.AI.ContentModeration.TextContentFilterSeverity.Violent
-api-type: winrt property
--->

#### TextContentFilterSeverity.Violent property

```
public Microsoft.Windows.AI.ContentModeration.SeverityLevel Violent { get; set; }
```

##### Property value

## See also

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows Copilot Runtime Sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsCopilotRuntime)
