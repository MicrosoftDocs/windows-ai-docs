---
title: Decrypt exported snapshots from Recall
description: Learn how to decrypt exported snapshot data from Windows Recall. This feature is only available to users located in the European Economic Area (EEA).
ms.author: mattwoj
author: mattwojo
ms.date: 06/13/2025
ms.topic: article
no-loc: [Recall]
---

# Decrypt exported snapshots from Recall

This guidance covers how to decrypt exported snapshots from Windows Recall and only applies to devices in the European Economic Area (EEA) where the Recall snapshot export feature is supported. Export of Recall and snapshot information is a user-initiated process and is per user. IT admins or other users can't initiate an export on behalf of another. [Learn more about Microsoft compliance with European Union (EU) General Data Protection Regulation (GDPR) regulating the transfer of customer personal data](/compliance/regulatory/offering-eu-model-clauses).

Windows **Recall** utilizes [Windows AI Foundry](../overview.md) to help you find anything you've seen on your PC by storing "snapshots" of this data. If you’re in the EEA, you can choose to export your Recall snapshots. Exporting allows you to share your Recall and snapshot information with third-party apps or websites that you trust. Exporting is optional, and you can review your Recall information at any time in Recall without needing to export.

Learn more about how to export Recall snapshots:

- [Export Recall snapshots with an export code](https://support.microsoft.com/topic/680bd134-4aaa-4bf5-8548-a8e2911c8069)
- [Manage Recall: Allow export of Recall and snapshot information](/windows/client-management/manage-recall#allow-export-of-recall-and-snapshot-information): IT Administrator guidance on how to manage Recall settings for users within your company, including the ability to export Recall snapshot data.
- [Configuration Service Provider (CSP) Policy for Windows AI: Allow Recall Export](/windows/client-management/mdm/policy-csp-windowsai#allowrecallexport): Guidance for IT administrators to establish the policy settings that determine whether the optional Recall feature is available for end users to enable on their device, including the policy for enabling the export of snapshot data.

Exported data from Windows Recall snapshots is encrypted. Learn how to use the decryption tool to view exported Recall snapshots below.

## Prerequisites

Recall is only available on Copilot+ PC devices and requires that you run the latest [Windows Insider Program](https://www.microsoft.com/windowsinsider) preview build.

Before you begin, ensure you have the following:

- The **export code** associated with your data export.
- The **input folder** containing your previously exported encrypted data.
- The **output folder** where decrypted files will be saved.
- A C++ development environment such as **Visual Studio** (optional, for building from source).

## How to export and view Recall snapshot data using the decryption tool

The Recall decryption tool processes securely encrypted data exports and outputs them into a human-readable format. Each snapshot is decrypted into a `.jpg` image, and its corresponding metadata is saved as a `.json` file.

To use Recall decryption tool:

1. Open a command line with Windows Terminal or PowerShell and clone the repo containing the tool: `git clone https://repo.git` [TO-DO need github repo URL where this tool is hosted](..).

2. Navigate to the folder <TO-DO: where will this be in the repo?> containing the <TO-DO: name of the decrypt tool executable>.exe

3. Run the tool from the command line with the required arguments: `.\RecallSnapshotsExport.exe "inputfolderpath" "outputfolderpath" "exportcode"`

The expected output will include:

- Decrypted snapshots saved as `.jpg` files.
- Corresponding metadata saved as `.json` files.
- Both file types will share the same filename and be located in the specified output folder.

[TO-DO: need a screenshot of example output]
