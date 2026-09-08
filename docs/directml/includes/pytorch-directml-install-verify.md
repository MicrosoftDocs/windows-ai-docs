## Install PyTorch with DirectML

Install the `torch-directml` package:

```console
pip install torch-directml
```

## Verify the installation

Start Python and run the following code to add two tensors on the DirectML device:

```python
import torch
import torch_directml

dml = torch_directml.device()
tensor1 = torch.tensor([1]).to(dml)
tensor2 = torch.tensor([2]).to(dml)
dml_algebra = tensor1 + tensor2
print(dml_algebra.item())
```

Expected output:

```output
3
```

## Samples and feedback

See the [DirectML PyTorch samples](https://github.com/microsoft/DirectML/tree/master/PyTorch) for examples. To report package issues or request features, use the [DirectML issue tracker](https://github.com/microsoft/DirectML/issues).
