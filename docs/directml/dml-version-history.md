---
title: DirectML version history
description: DirectML is distributed as a system component of Windows, and is available as part of the Windows operating system (OS) in Windows 10, version 1903 (10.0; Build 18362) and newer.
ms.topic: article
ms.date: 08/26/2024
author: stevewhims
ms.author: stwhi
---

# DirectML version history

DirectML is distributed as a system component of Windows, and is available as part of the Windows operating system (OS) in Windows 10, version 1903 (10.0; Build 18362) and newer.

Starting with DirectML version 1.4.0, DirectML is also available as a standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/)), which is useful for applications that wish to use a fixed version of DirectML, or when running on older versions of Windows 10.

DirectML follows the [semantic versioning](https://semver.org/) conventions. That is, version numbers follow the form `major.minor.patch`. The first release of DirectML has a version of 1.0.0.

## Version table

|DirectML version|Feature level supported (see [DirectML feature level history](dml-feature-level-history.md))|DML_TARGET_VERSION|First available in (OS)|First available in (Redistributable)|
|-|-|-|-|-|
|1.15.4|[DML_FEATURE_LEVEL_6_4](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_4)|`0x6400`|N/A|[DirectML-1.15.4](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.15.4)|
|1.15.3|[DML_FEATURE_LEVEL_6_4](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_4)|`0x6400`|N/A|[DirectML-1.15.3](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.15.3)|
|1.15.2|[DML_FEATURE_LEVEL_6_4](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_4)|`0x6400`|N/A|[DirectML-1.15.2](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.15.2)|
|1.15.1|[DML_FEATURE_LEVEL_6_4](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_4)|`0x6400`|N/A|[DirectML-1.15.1](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.15.1)|
|1.15.0|[DML_FEATURE_LEVEL_6_4](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_4) and [DML_FEATURE_LEVEL_6_3](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_3)|`0x6400` or `0x6300`|N/A|[DirectML-1.15.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.15.0)|
|1.13.1|[DML_FEATURE_LEVEL_6_2](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_2)|`0x6200`|N/A|[DirectML-1.13.1](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.13.1)|
|1.13.0|[DML_FEATURE_LEVEL_6_2](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_2)|`0x6200`|N/A|[DirectML-1.13.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.13.0)|
|1.12.0|[DML_FEATURE_LEVEL_6_1](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_1)|`0x6100`|N/A|[DirectML-1.12.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.12.0)|
|1.11.0|[DML_FEATURE_LEVEL_6_0](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_0)|`0x6000`|N/A|[DirectML-1.11.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.11.0)|
|1.10.0|[DML_FEATURE_LEVEL_5_2](/windows/ai/directml/dml-feature-level-history#dml_feature_level_5_2)|`0x5200`|N/A|[DirectML-1.10.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.10.0)|
|1.9.0|[DML_FEATURE_LEVEL_5_1](/windows/ai/directml/dml-feature-level-history#dml_feature_level_5_1)|`0x5100`|N/A|[DirectML-1.9.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.9.0)|
|1.8.0|[DML_FEATURE_LEVEL_5_0](/windows/ai/directml/dml-feature-level-history#dml_feature_level_5_0)|`0x5000`|Windows 11 (Build 10.0.22621; 22H2)|[DirectML-1.8.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.8.0)|
|1.7.0|[DML_FEATURE_LEVEL_4_1](/windows/ai/directml/dml-feature-level-history#dml_feature_level_4_1)|`0x4100`|N/A|[DirectML-1.7.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.7.0)|
|1.6.0|[DML_FEATURE_LEVEL_4_0](/windows/ai/directml/dml-feature-level-history#dml_feature_level_4_0)|`0x4000`|Windows 11 (Build 10.0.22000; 21H2)|[DirectML-1.6.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.6.0)|
|1.5.0|[DML_FEATURE_LEVEL_3_1](/windows/ai/directml/dml-feature-level-history#dml_feature_level_3_1)|`0x3100`|N/A|[DirectML-1.5.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.5.0)|
|1.4.0<sup>1</sup>|[DML_FEATURE_LEVEL_3_0](/windows/ai/directml/dml-feature-level-history#dml_feature_level_3_0)|`0x3000`|N/A|[DirectML-1.4.0](https://www.nuget.org/packages/Microsoft.AI.DirectML/1.4.0)|
|1.1.0|[DML_FEATURE_LEVEL_2_0](/windows/ai/directml/dml-feature-level-history#dml_feature_level_2_0)|`0x2000`|Windows 10, version 2004 (10.0; Build 19041) (Windows 10 May 2020 Update). Aka "20H1".|N/A|
|1.0.0|[DML_FEATURE_LEVEL_1_0](/windows/ai/directml/dml-feature-level-history#dml_feature_level_1_0)|`0x1000`|Windows 10, version 1903 (10.0; Build 18362) (Windows 10 May 2019 Update). Aka "19H1".|N/A|

<sup>1</sup> The 1.2.0 and 1.3.0 intermediate releases of DirectML weren't made widely available.

## Selecting a DirectML target version

For convenience, certain features in the `DirectML.h` header file are declared conditionally based on the value of the `DML_TARGET_VERSION` macro. By setting the `DML_TARGET_VERSION` macro to certain values, you can exclude parts of `DirectML.h` from your application.

That can be helpful if you're using a newer copy of `DirectML.h`, but you're targeting a lower version of the DirectML binary, because it ensures that any attempt to use features beyond the chosen target level won't compile. This mechanism is similar to the `NTDDI_VERSION` macro (see [Macros for conditional declarations](/windows/win32/winprog/using-the-windows-headers#macros-for-conditional-declarations)).

Here are the valid values for the `DML_TARGET_VERSION` macro.

|DML_TARGET_VERSION|Effect|
|-|-|
|`0x6400`|Any features that require a version of DirectML newer than **1.15.0** are excluded from `DirectML.h`. |
|`0x6300`|Any features that require a version of DirectML newer than **1.15.0**, or that are [DML_FEATURE_LEVEL_6_4](/windows/ai/directml/dml-feature-level-history#dml_feature_level_6_4) features, are excluded from `DirectML.h`. |
|`0x6200`|Any features that require a version of DirectML newer than **1.13.0** are excluded from `DirectML.h`.|
|`0x6100`|Any features that require a version of DirectML newer than **1.12.0** are excluded from `DirectML.h`.|
|`0x6000`|Any features that require a version of DirectML newer than **1.11.0** are excluded from `DirectML.h`.|
|`0x5200`|Any features that require a version of DirectML newer than **1.10.0** are excluded from `DirectML.h`.|
|`0x5100`|Any features that require a version of DirectML newer than **1.9.0** are excluded from `DirectML.h`.|
|`0x5000`|Any features that require a version of DirectML newer than **1.8.0** are excluded from `DirectML.h`.|
|`0x4100`|Any features that require a version of DirectML newer than **1.7.0** are excluded from `DirectML.h`.|
|`0x4000`|Any features that require a version of DirectML newer than **1.6.0** are excluded from `DirectML.h`.|
|`0x3100`|Any features that require a version of DirectML newer than **1.5.0** are excluded from `DirectML.h`.|
|`0x3000`|Any features that require a version of DirectML newer than **1.4.0** are excluded from `DirectML.h`.|
|`0x2000`|Any features that require a version of DirectML newer than **1.1.0** are excluded from `DirectML.h`.|
|`0x1000`|Any features that require a version of DirectML newer than **1.0.0** are excluded from `DirectML.h`.|
|*Not set*|The target version is selected automatically for you. See below for details.|

If `DML_TARGET_VERSION` is not set, then it is selected automatically by the following.

* If the `DML_TARGET_VERSION_USE_LATEST` macro is defined, then the latest target version is selected.
* Otherwise, the target version is selected based on the value of the `NTDDI_VERSION` macro.
  *  `NTDDI_WIN10_ZN` results in a target version of `0x6000`.
  *  `NTDDI_WIN10_NI` results in a target version of `0x5000`.
  *  `NTDDI_WIN10_CO` results in a target version of `0x4000`.
  *  `NTDDI_WIN10_FE` results in a target version of `0x3000`.
  *  `NTDDI_WIN10_VB` results in a target version of `0x2000`.
  *  `NTDDI_WIN10_19H1` results in a target version of `0x1000`.
  *  If `NTDDI_VERSION` is not defined, then the latest target version is selected (as if `DML_TARGET_VERSION_USE_LATEST` were specified).

### Example

Consider an application using version 10.0.19041.0 (Windows 10, version 2004) of the Windows Software Development Kit (SDK). From the table above, the version of DirectML that this corresponds to is 1.1.0, and the corresponding `DML_TARGET_VERSION` is `0x2000`.

If you don't set either the `DML_TARGET_VERSION` nor the `NTDDI_VERSION` macros, then the selected target version will default to `0x2000`, and everything in `DirectML.h` will be available for use.

If you want your application to be able to run on Windows 10, version 1903 (10.0; Build 18362), then you could `#define DML_TARGET_VERSION 0x1000`, which will exclude all content in `DirectML.h` that isn't supported by DirectML version 1.0.0. This ensures that any attempt to use functionality that requires a greater version will fail to compile.

## DirectML version versus feature level

The DirectML version (for example, 1.0.0, or 1.4.0) describes a particular release of DirectML, including its associated `DirectML.h` header file and `.lib` file.

The feature level (for example, `DML_FEATURE_LEVEL_1_0`, or `DML_FEATURE_LEVEL_2_0`) describes the capability of the underlying implementation of the API, which can vary from the version of `DirectML.h` used.

For example, an application building against a newer SDK, but running on an older version of Windows, might (at runtime) see a lower supported feature level, even if it's compiled against the latest SDK.

## See also

* [Windows AI](../index.yml)
* [DirectML feature level history](dml-feature-level-history.md)
* [DML_FEATURE_LEVEL enumeration](/windows/win32/api/directml/ne-directml-dml_feature_level)
* [Microsoft.AI.DirectML redistributable package](https://www.nuget.org/packages/Microsoft.AI.DirectML/)
