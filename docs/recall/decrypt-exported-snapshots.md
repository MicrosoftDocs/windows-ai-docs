---
title: Decrypt exported snapshots from Recall
description: Learn how to decrypt exported snapshot data from Windows Recall. This feature is only available to users located in the European Economic Area (EEA).
ms.author: mattwoj
author: mattwojo
ms.date: 06/17/2025
ms.topic: article
no-loc: [Recall]
---

# Decrypt exported snapshots from Recall

To access exported snapshots from Recall users for use in an application, developers will need to build a decryption process into the app. This guide provides an explanation of the decryption process and associated code samples.

Exporting Recall snapshots is only supported on devices in the European Economic Area (EEA). Export of Recall snapshots is a user-initiated process and is per user. Exported snapshots are encrypted.  

Learn more about how to [Export Recall snapshots](https://support.microsoft.com/windows/export-recall-snapshots-680bd134-4aaa-4bf5-8548-a8e2911c8069) or see the [Recall overview](index.md) for more about how this AI-backed feature works.

## Prerequisites

The option to export Recall snapshots is only available on [Copilot+ PC devices](https://www.microsoft.com/windows/copilot-plus-pcs) in the European Economic Area (EEA) that are running the latest [Windows Insider Program preview build](https://www.microsoft.com/windowsinsider).

To decrypt exported snapshots from Recall, ensure the following:

- The user must first [Export Recall snapshots](https://support.microsoft.com/windows/export-recall-snapshots-680bd134-4aaa-4bf5-8548-a8e2911c8069) using the Recall export code that they were provided. The user will to provide the location (folder path) containing these previously exported (and encrypted) snapshots.
- The user will need to provide the 32-character Recall export code associated with their snapshots in order to decrypt those snapshots.
- The location (output folder path) will need to be defined where the decrypted .jpg and .json files associated with the user’s exported snapshots will be saved.

## How to decrypt exported Recall snapshots

Sample code for decrypting exported Recall snapshots can be found in the [RecallSnapshotsExport GitHub Repo](https://github.com/microsoft/RecallSnapshotsExport). The decryption process associated with the code sample is explained below.

### Compute Export Key

The user will need to provide the location (folder path) where their exported Recall snapshots have been saved, in addition to the Recall export code that they were asked to save during the initial Recall setup. The Recall export code looks something like: `0a0a0a0a-1111-bbbb-2222-3c3c3c3c3c3c`

First, remove the dash – to result in a 32-character string ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L307-L321)): `0a0a0a0a1111bbbb22223c3c3c3c3c3c`

Next, build an array containing the byte value for each pair of hex digits in turn. Using C++ you end up with something like ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L171-L187)):
`uint8_t[] exportCodeBytes = { 0x1a, 0x2b, 0x3c, 0x4d, 0x5e, 0x6f, 0x7a, 0x8b, 0x9c, 0x0d, 0x1e, 0x2f, 0x3a, 0x4b, 0x5c, 0x6d };`

Then, take that array and compute the SHA256 hash, which results in a 32-byte value, which is the export key. Now any number of snapshots can be decrypted using the resulting export key ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L200-L208)).

### Decrypt the encrypted snapshots

The layout of a snapshot (in little-endian format): `| uint32_t version | uint32_t encryptedKeySize | uint32_t encryptedContentSize | uint32_t contentType | uint8_t[KeySIze] encryptedContentKey | uint8_t[ContentSize] encryptedContent |`

First, read the four [uint32_t values](/cpp/c-runtime-library/standard-types?view=msvc-170#fixed-width-integral-types-stdinth)([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L222-L228)).

Next, verify that version has the value, 2 ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L230-L233)).

Then, read the encryptedKeyContent ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L235-L236)).

Decrypt the encryptedKeyContent ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L138-L169)) using the exportKey ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L210-L212)) to get the contentKey ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L237)) (crypto algorithm is AES_GCM).

Read the encryptedContent ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L239-L240)).

Decrypt the encryptedContent ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L113-L136)) with the contentKey (crypto algorithm is AES_GCM) ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L241)).

Output decrypted Recall snapshot content in the form of a .jpg image with corresponding .json metadata into the designated folder path ([see sample code](https://github.com/microsoft/RecallSnapshotsExport/blob/5442b3a90aed91888a67c283c3132290ecddb044/RecallSnapshotsExport.cpp#L251)).

The expected output will include:

- Decrypted snapshots saved as .jpg files.
- Corresponding metadata saved as .json files.

Both file types will share the same filename and be found in the specified output folder.  

## Learn more about Recall

- [Learn more about Windows Recall](index.md).
- [Export Recall snapshots with an export code](https://support.microsoft.com/topic/680bd134-4aaa-4bf5-8548-a8e2911c8069)
- [Manage Recall: Allow export of Recall and snapshot information](/windows/client-management/manage-recall#allow-export-of-recall-and-snapshot-information): IT Administrator guidance on how to manage Recall settings for users within your company, including the ability to export Recall snapshot data.
- [Configuration Service Provider (CSP) Policy for Windows AI: Allow Recall Export](/windows/client-management/mdm/policy-csp-windowsai#allowrecallexport): Guidance for IT administrators to establish the policy settings that determine whether the optional Recall feature is available for end users to enable on their device, including the policy for enabling the export of snapshot data.
