---
author: drewbatgit
title: Copilot in Windows
description: Learn how to determine if Copilot in Windows is available on the current device and launch it using the Microsoft Edge launch URI scheme.
ms.author: drewbat
ms.date: 04/10/2024
ms.topic: article
keywords: windows 11, copilot, copilot in windows, windows ai, windows ml, winml, windows machine learning
---

# Copilot in Windows

This article describes how to determine if Copilot in Windows is available on the current device and, if the feature is available, how to launch Copilot in Windows using the Microsoft Edge launch URI scheme. The techniques discussed in this article are intended for use in the near term. In future releases, the methods described in this article may be deprecated and replaced with more formal and robust APIs. For general information and guidance on using Microsoft Copilot, see [Microsoft Copilot documentation and resources](/copilot), or [Overview of Copilot with commercial data protection](/copilot/overview).

On Windows 11, version 23H2, this feature is available on Build 22631.3007 or later. On Windows 11, version 22H2, the feature is available on Build 22621.3007 or later. This feature requires Microsoft Edge version 120.0.2210.121 or later.

## Check Copilot in Windows availability on the device

To determine whether Copilot in Windows is available on a Windows device, check the value of the following registry key. A value of 0 means that Copilot in Windows is not available. A value of 1 means that Copilot in Windows is available.

| Item | Value |
|------|-------|
| Registry path | HKEY_CURRENT_USER\Software\Microsoft\Windows\Shell\Copilot |
| Registry key name | IsCopilotAvailable |
| Possible values | 0 – Not Available or 1 - Available |

## Launch Copilot in Windows using the Microsoft Edge launch URI scheme

Once it is determined that Copilot in Windows is available on the current device, applications can invoke the feature using the Microsoft Edge launch URI scheme, `microsoft-edge://?ux=copilot&lcp=1`.

This URI scheme supports the following query string parameters that are related to Copilot in Windows

| Parameter | Value | Description | Required |
|-----------|-------|-------------|----------|
| ux        | copilot | Launches Microsoft Edge in the Copilot in Windows context. | Yes |
| lcp       | 1 | Launches Microsoft Edge in the Copilot in Windows context. | Yes |
| prompt    | A string | An optional string representing the prompt that will be passed to Copilot in Windows on launch. | No |
| formcode  | A string | An optional 6-digit identifier that can be passed through to help identify incoming traffic. | No |
| cpfilepath  | A string | An optional string containing the file path of the file that will be passed to Copilot. | No |
| cpfilemime  | A string | An optional string containing the MIME type of the file that will be passed to Copilot.  | No |

The Copilot in Windows has the following syntax requirements:

- In order to add on to the protocol, each key/value pair should be separated by an ampersand.
- The prompt string must be URI-encoded / URI-escaped.
- The prompt string portion of the URI must be less than or equal to 2000 characters long before URI encoding.

### Examples


The following is an example URI to launch Copilot in Windows.

`microsoft-edge://?ux=copilot&lcp=1`

Use the *prompt* query string parameter to specify a prompt.

`microsoft-edge://?ux=copilot&lcp=1&prompt=Explain%20This%3A%2020%2F20%20Vision`

Specify the *formcode* parameter 123456.

`microsoft-edge://?ux=copilot&lcp=1&prompt=Explain%20This%3A%2020%2F20%20Vision&formcode=123456`

Use the *cpfilepath* and *cpfilemime* to specify a file that will be passed to Copilot.

`microsoft-edge://?ux=copilot&lcp=1&prompt=What%20is%20this%20a%20picture%20of%3F&formcode=123456&cpfilepath=C%3A%5CUsers%5Cme%5CDownloads%5CCopilot.png&cpfilemime=image%2Fpng`
