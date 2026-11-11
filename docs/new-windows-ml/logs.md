---
title: Capture Windows ML logs
description: Learn how to capture and analyze Windows ML diagnostic logs using Event Tracing for Windows (ETW) to debug model loading, execution provider selection, and inference issues.
ms.date: 11/11/2026
ms.topic: how-to
---

# Capture Windows ML logs

This guide walks you through capturing Windows ML diagnostic data using Event Tracing for Windows (ETW) and generating rundown logs. These logs are useful for:

- Debugging model loading and inference issues
- Understanding execution provider (EP) selection and behavior
- Identifying driver and hardware compatibility issues
- Sharing detailed diagnostic data with the Windows ML team

## Prerequisites

- **Administrator privileges** (required for Windows Performance Toolkit installation and for starting/stopping ETW tracing with WPR)
- **PowerShell 5.1 or later**
- **Windows 10 version 1903 or later** (recommended)
- An application using Windows ML, or a test case to trace

## Step 1: Download the required files

Download the following files from the [WindowsML/Tools/capture-logs](https://github.com/microsoft/WindowsML/tree/main/Tools/capture-logs) folder on GitHub:

| File | Description |
|------|-------------|
| [`Get-WinMLRundown.ps1`](https://github.com/microsoft/WindowsML/blob/main/Tools/capture-logs/Get-WinMLRundown.ps1) | PowerShell script for generating rundown logs |
| [`WinML.wprp`](https://github.com/microsoft/WindowsML/blob/main/Tools/capture-logs/WinML.wprp) | WPR profile for capturing Windows ML diagnostic data |
| [`WindowsMLProfile.wpaProfile`](https://github.com/microsoft/WindowsML/blob/main/Tools/capture-logs/WindowsMLProfile.wpaProfile) | WPA profile for processing ETL files |

> [!TIP]
> To download a file from GitHub, click its link to open the file, then click the **download** button near the top-right of the file view.

Save all three files to the same folder. You'll run commands from this folder in the following steps.

## Step 2: Install Windows Performance Toolkit

The Windows Performance Toolkit is part of the Windows SDK for Windows 11, and contains `wpaexporter.exe`, which is required to process ETL files.

If `wpaexporter.exe` isn't installed, `Get-WinMLRundown.ps1` tries to install it when you run it in Step 7. To install it yourself, follow these steps:

1. Download the latest version of the [Windows SDK for Windows 11](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/).

2. Install the Windows SDK, ensuring that the **Windows Performance Toolkit** feature is selected.

3. Verify installation by checking that `wpaexporter.exe` exists at one of the following paths:
   - `%ProgramFiles%\Windows Kits\10\Windows Performance Toolkit\wpaexporter.exe`
   - `%ProgramFiles(x86)%\Windows Kits\10\Windows Performance Toolkit\wpaexporter.exe`

## Step 3: Open PowerShell as Administrator

Open PowerShell as Administrator (required for ETW operations) and navigate to the folder containing the required files.

## Step 4: Start ETW tracing with WPR

> [!NOTE]
> If you already have an ETL file, skip to [Step 7: Generate rundown logs](#step-7-generate-rundown-logs).

Use Windows Performance Recorder (WPR) with the Windows ML-specific profile to capture diagnostic data.

Start tracing with the Windows ML profile:

```powershell
wpr -start .\WinML.wprp -filemode
```

Start tracing before you start your application when you can. With Windows ML 2.8 and later, a trace that starts after your application is running still includes the [Windows ML runtime state](#windows-ml-runtime-state).

## Step 5: Run your application

With tracing active, run your application that uses Windows ML, or your test scenario.

> [!IMPORTANT]
> Keep the tracing session as short as possible to minimize ETL file size and processing time.

## Step 6: Stop ETW tracing

Stop the tracing session and save the ETL file:

```powershell
wpr -stop winml_trace.etl
```

This creates `winml_trace.etl` in the current directory containing all captured events.

## Step 7: Generate rundown logs

Process the ETL file to generate human-readable rundown logs:

```powershell
.\Get-WinMLRundown.ps1 -EtlFilePath "winml_trace.etl" -WpaProfilePath ".\WindowsMLProfile.wpaProfile" -OutputFolder ".\rundown_output"
```

> [!NOTE]
> If you get an error that the script cannot be run because it is not digitally signed, change the execution policy for the current PowerShell session and then re-run the command above:
>
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Process
> ```

### Script parameters

| Parameter | Description |
|-----------|-------------|
| `-EtlFilePath` | Path to the ETL file captured in Step 6. |
| `-WpaProfilePath` | Path to the WPA profile (use the provided `WindowsMLProfile.wpaProfile` file). |
| `-OutputFolder` | Directory where the rundown log will be created. |

### Output files

The script generates output in the specified `-OutputFolder` directory (for example, `rundown_output`):

- **`rundown_output\WinmlRundown.log`** - Main rundown log with categorized diagnostic data.
- **Temporary CSV files** - Deleted after the log is written. They're kept if the script stops with an error or finds no data.

## Understanding the rundown log

The generated `WinmlRundown.log` contains organized diagnostic data grouped by process.

### Process organization

Each section begins with a process header:

```
Process: YourApplication.exe (12345)
----------------------------------------
```

### Event categories

#### ONNX Runtime version

```
Onnx Version (2024/01/15 10:30:45.123):
    Runtime Version = 1.23.2
    Is Redist = true
    Framework Name = WinAI
```

Shows the ONNX Runtime version and configuration. Indicates whether the redistributable version is in use and identifies the framework.

#### WindowsAppSDK.ML Version

```
WindowsAppSDK.ML Version (2024/01/15 10:30:45.100):
    Version = 2.0.0
```

Shows the Windows App SDK Windows ML version used by the application. Duplicate entries with the same version are suppressed.

#### Windows ML runtime state

```
WinML Runtime State (2024/01/15 10:31:10.000):
    WinML Version = 2.8.0
    ORT Version = 1.30.0
    Registered EPs = 1
```

Logged by Windows ML 2.8 and later when tracing starts and when the trace is saved. Shows the Windows ML version and, after Windows ML loads ONNX Runtime, the ONNX Runtime version. Each execution provider that Windows ML registered is listed in a `WinML Registered EP` entry.

#### Driver info

```
Driver Info (2024/01/15 10:30:45.200):
    Device Class = GPU
    Driver Name = 
    Driver Version = 
```

Hardware and driver information for GPU/NPU devices. This is critical for diagnosing execution provider compatibility and hardware-specific issues.

#### EP library registration

```
EP Library Registration (2024/01/15 10:30:45.300):
    EP Name = DmlExecutionProvider
    Success = True
```

Shows each execution provider library registered with ONNX Runtime, and whether it loaded.

#### Session creation

```
Session Creation (2024/01/15 10:30:45.456):
    Schema Version = 0
    Session ID = 1
    IR Version = 8
    ORT Programming Projection = 1
    Using FP16 = false
    Model Weight Type = FLOAT: 89, UINT8: 36, INT8: 104
    Model Graph Hash = abc123...
    Model Weight Hash = def456...
    EP ID = CPUExecutionProvider
```

Shows model loading and session initialization details, including execution provider selection, model characteristics (precision, weight type), and unique identifiers for debugging.

#### EP auto selection

```
EP Auto Selection (2024/01/15 10:30:45.500):
    Schema Version = 0
    Session ID = 1
    Selection Policy = PREFER_NPU
    Requested EP = CPUExecutionProvider
    Available EP = DmlExecutionProvider, CPUExecutionProvider
```

Shows the execution provider selection process, including what was requested versus what's available. The selection policy determines prioritization.

#### EP device usage

```
EP Device Usage (2024/01/15 10:30:45.600):
    Schema Version = 1
    Session ID = 1
    Execution Provider = DmlExecutionProvider
    Device Type = GPU
    Assigned Nodes = 1
```

Shows the device (NPU, GPU, or CPU) that each execution provider uses in a session, and how many model nodes it was assigned. Requires ONNX Runtime 1.27 or later.

#### CPU fallback

```
CPU Fallback (2024/01/15 10:30:45.600):
    Session ID = 1
    Result = PARTIAL - 1 of 2 nodes ran on CPU
```

Summarizes the EP device usage entries for each session:

| Result | Meaning |
|--------|---------|
| `NONE` | No nodes ran on the CPU. |
| `PARTIAL` | Some nodes ran on the CPU. |
| `FULL` | All nodes ran on the CPU, although a GPU or NPU was requested. |
| `N/A` | The session uses only CPU execution providers, and the trace shows no GPU or NPU request. |
| `UNKNOWN` | The trace doesn't show how many nodes ran on the CPU. |

#### Registered providers

```
Registered Providers (2024/01/15 10:30:45.100):
    PackageFamilyName = 
```

Lists execution provider packages that Windows ML registered. Only older Windows ML versions log this entry.

#### Error events

```
====================WINML ONNX ERROR=================== (2024/01/15 10:30:46.000):
    Schema Version = 1
    HRESULT = 0x80070057
    Session ID = 42
    Error Code = 2
    Error Category = 2
    Error Message = Invalid input tensor dimensions
    File = ModelBinding.cpp
    Function = BindInput
    Line = 245

====================ONNX ERROR=================== (2024/01/15 10:30:46.100):
    Location = ...
    Message = ...
```

Provides detailed error information with source code context, HRESULT codes, and system-level error details.

## Troubleshooting

### "wpaexporter.exe not found"

Follow the installation steps in [Step 2](#step-2-install-windows-performance-toolkit) to install Windows Performance Toolkit. Verify installation by checking the paths shown in that step. Ensure you ran PowerShell as Administrator during installation.

### "wpaexporter.exe failed with exit code"

Check that `-EtlFilePath` points to a complete ETL file and `-WpaProfilePath` points to `WindowsMLProfile.wpaProfile`.

### "Access denied" when starting WPR

Run PowerShell as Administrator. ETW operations require elevated privileges.

### Script is not digitally signed

If you get an error that `Get-WinMLRundown.ps1` cannot be run because it is not digitally signed, change the execution policy for the current PowerShell session:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Process
```

Then, try running [Step 7](#step-7-generate-rundown-logs) again.

### Large ETL files

Keep tracing sessions short. The `-filemode` parameter is used to write traces directly to disk.

## Tips

- **Minimize trace duration**: Start tracing just before running your scenario and stop immediately after.
- **Use specific scenarios**: Focus on reproducing specific issues rather than general application usage.
- **Clean environment**: Close unnecessary applications to reduce noise in traces.

## See also

- [Troubleshoot execution provider download issues](execution-provider-errors.md)
- [Get started with Windows ML](get-started.md)
