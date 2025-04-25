---
title: Adding sensitivity labels to Recall snapshots
description: Learn how to add enterprise-level Data Loss Protection sensitivity labels to the snapshots Recall captures from your app.
ms.author: aleader
author: aleader
ms.date: 04/25/2025
ms.topic: article
no-loc: [Recall, Purview, DLP, Data Loss Prevention]
---

# Adding sensitivity labels to Recall snapshots

If your app integrates with enterprise policies of Data Loss Prevention (DLP) solutions such as Purview, your app can share sensitivity label information with Recall to help Recall enforce DLP policies - such as not capturing a particular window or preventing users from extracting data from snapshots.

## What your app provides

Your app can provide the following data within a UserActivity that represents the current content the user is viewing:

- **Sensitivity Label Id**: A unique identifier associated with the label assigned to the content.
- **Organization ID**: A unique identifier for the organization or tenant associated with the label (also referred to as tenant ID or item ID).

## How Recall uses that data

Using these two data points, Recall can query the DLP provider for detailed information such as:

- **Label Description**: Includes the label name (e.g., Confidential, Highly Confidential) and metadata (color, tooltip, sensitivity value).
- **DLP Restrictions**: Currently, the two relevant restrictions are:
- **CopyToClipboard**: If set to Block, Recall will prevent users from extracting text or images from a snapshot.
- **ScreenClip**: If set to Block, Recall will not capture the specific window with the label. 


## How to provide this data

Your app provides this info through the UserActivity API described above, which is also used for supporting relaunch of the content.

This information is specified within the ContentInfo property of the UserActivity class.

The ContentInfo property is a JSON object. To provide sensitivity label info, construct a JSON object similar to the one below...

### Sample UserActivity.ContentInfo value

```json
{
  "additionalProperty": [
    {
      "@type": "PropertyValue",
      "name": "SensitivityLabelID",
      "value": "F96E0B19-8C3A-4D5A-8B9A-2E8CFC43247B"
    },
    {
      "@type": "PropertyValue",
      "name": "OrganizationID",
      "value": "D3FE4C20-9C77-45AB-A8E7-9870D3C9C856"
    }
  ]
}
```