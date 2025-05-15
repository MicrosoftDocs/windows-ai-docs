---
title: Python binding
description: Windows Machine Learning python binding
ms.date: 05/14/2025
ms.topic: article
---

# Python binding

Windows Machine Learning provides a python binding along with the nuget release, in the format of "onnxruntime-winml". This is a special version of onnxruntime that has the WinML execution provider management feature enabled. Making the experience of using auto-EP in onnxruntime smoother and easier.

## Installation

The package is available for both ARM64 and x64 Windows machines. With support for Python ranging from 3.10 to 3.13. The package can be installed using pip:

```powershell
pip install --index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple --extra-index-url https://pypi.org/simple onnxruntime-winml
```

## How it works

When imported, this package performs the following steps:

1. Checks if the WinML runtime package is installed. If not, it installs the included version.

2. Detects and verifies whether suitable EPs for the local machine are installed. If not, it downloads and installs them.

3. Registers the installed EPs.

After these steps, users can use the onnxruntime's auto-EP feature without the need to manually register EPs.

## Example

There are two ways to use onnxruntime-winml.

### Auto-EP

```Python
# Download, install and register the suitable EPs.
import onnxruntime as ort

# Set the preferred device to NPU.
options = ort.SessionOptions()
options.set_provider_selection_policy(ort.OrtExecutionProviderDevicePolicy.PREFER_NPU)
assert options.has_providers()

session = ort.InferenceSession(
    "path_to_your_model.onnx",
    sess_options=options,
)
```

### Specify EP

Be aware that EPs registered by WinML cannot be accessed via the old "providers" option. Please follow the example below to specify an EP.

```Python
# Download, install and register the suitable EPs.
import onnxruntime as ort

# Select a specific EP.
def add_ep_for_device(session_options, ep_name, device_type, ep_options=None):
    ep_devices = ort.get_ep_devices()
    for ep_device in ep_devices:
        if ep_device.ep_name == ep_name and ep_device.device.type == device_type:
            session_options.add_provider_for_devices([ep_device], {} if ep_options is None else ep_options)

options = ort.SessionOptions()
add_ep_for_device(options, "QNNExecutionProvider", ort.OrtHardwareDeviceType.NPU)  # example for QNN NPU
assert options.has_providers()

session = ort.InferenceSession(
    "path_to_your_model.onnx",
    sess_options=options,
)
```
