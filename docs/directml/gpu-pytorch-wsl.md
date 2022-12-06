---
title: Enable PyTorch with DirectML on WSL 2
description: This preview provides students and beginners a way to start building your knowledge in the machine-learning (ML) space on your existing hardware by using the **PyTorch with DirectML** package.
ms.topic: article
ms.date: 10/12/2021
---

# Enable PyTorch with DirectML on WSL 2

This preview provides students and beginners a way to start building your knowledge in the machine-learning (ML) space on your existing hardware by using the **PyTorch with DirectML** package. Once set up, you can start with our [samples](https://github.com/microsoft/DirectML/tree/master/PyTorch).

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Check your version of Windows 

The **PyTorch with DirectML** package on native Windows Subsystem for Linux (WSL) works starting with Windows 11. You can check your build version number by running `winver` via the **Run** command (Windows logo key + R).

## Check for GPU driver updates
Ensure you have the latest GPU driver installed. Select **check for updates** in the **Windows Update** section of the Settings app.

## Set up the PyTorch with DirectML preview

### Install WSL 2

To install the Windows Subsystem for Linux (WSL) 2, see the instructions in [Install WSL](/windows/wsl/install).

Then install the WSL GUI driver by following the instructions in the `README.md` file in the [microsoft/wslg](https://github.com/microsoft/wslg) GitHub repository.

### Set up a Python environment 

We recommend that you set up a virtual Python environment inside your WSL 2 instance. There are many tools that you can use to set up a virtual Python environment&mdash;in this topic we'll use Anaconda's [Miniconda](https://docs.conda.io/en/latest/miniconda.html). The rest of this setup assumes that you use a Miniconda environment.

Install Miniconda by following the [installation guidance](https://conda.io/projects/conda/en/latest/user-guide/install/index.html) on Anaconda's site, or by running the following commands in WSL.

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh 
bash Miniconda3-latest-Linux-x86_64.sh
```

Once Miniconda is installed, create a Python environment named *directml*, and activate it through the following commands:

```
conda create --name directml -y
conda activate directml
```

### Install PyTorch and the Torch-DirectML Plugin 

> [!NOTE]
> The PyTorch-directml package supports only PyTorch 1.13.

The latest release of Torch-DirectML follows a plugin model, meaning you have two packages to install. First, install the pytorch dependencies by running the following commands:

```
conda install numpy pandas tensorboard matplotlib tqdm pyyaml -y
pip install opencv-python
pip install wget
pip install torchvision
```

Then, install PyTorch. For our purposes you only need to install the cpu version, but if you need other compute platforms then follow the installation instructions on [PyTorch's website](https://pytorch.org/get-started/locally/).

```
conda install pytorch cpuonly -c pytorch
```

Finally, install the PyTorch-DirectML plugin.
```
pip install torch-directml
```

### Verification and Device Creation

Once you've installed the Torch-DirectML package, you can verify that it runs correctly by adding two tensors. First start an interactive Python session, and import Torch with the following lines:

```
import torch
import torch_directml
dml = torch_directml.device()
```
The current release of the Torch-DirectML plugin is mapped to the "PrivateUse1" Torch backend. The new torch_directml.device() API is a convenient wrapper for sending your tensors to the DirectML device.

With the DirectML device created, you can now define two simple tensors; one tensor containing a 1 and another containing a 2. Place the tensors on the "dml" device.

```
tensor1 = torch.tensor([1]).to(dml) # Note that dml is a variable, not a string!
tensor2 = torch.tensor([2]).to(dml)
```

Add the tensors together, and print the results.

```
dml_algebra = tensor1 + tensor2
dml_algebra.item()
```

You should see the number 3 being output, as in the example below.

```
>>> import torch
>>> tensor1 = torch.tensor([1]).to(dml)
>>> tensor2 = torch.tensor([2]).to(dml)
>>> dml_algebra = tensor1 + tensor2
>>> dml_algebra.item()
3
```  

## PyTorch with DirectML samples and feedback 

Now you're ready to start learning more about ML training. Check out the [our samples](https://github.com/microsoft/DirectML/tree/master/PyTorch) to get started. If you run into issues, or have feedback on the PyTorch with DirectML package, then please [connect with our team here](https://github.com/microsoft/DirectML/issues).
