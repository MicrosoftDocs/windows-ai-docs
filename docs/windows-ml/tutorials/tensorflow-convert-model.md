---
title: Convert your TensorFlow model into ONNX format
description: Learn how to convert your TensorFlow model into ONNX format, for use with Windows Machine Learning APIs.
ms.date: 5/8/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials, pytorch
---

# Convert TensorFlow model to ONNX

In [the previous step of this tutorial](tensorflow-train-model.md), we created a machine learning model with TensorFlow. Now, we'll convert it to the ONNX format.

Here, we'll use the `tf2onnx` tool to convert our model, following these steps.

1. Save the tf model in preparation for ONNX conversion, by running the following command.

`python save_model.py --weights ./data/yolov4.weights --output ./checkpoints/yolov4.tf --input_size 416 --model yolov4`

2. Install `tf2onnx` and `onnxruntime`, by running the following commands.

```
pip install onnxruntime
pip install git+https://github.com/onnx/tensorflow-onnx
```

3. Convert the model, by running the following command.

`python -m tf2onnx.convert --saved-model ./checkpoints/yolov4.tf --output model.onnx --opset 11 --verbose`

## Next steps

We've now converted our model to an ONNX format, suitable for use with Windows Machine Learning APIs. In the final stage of this tutorial, we [integrate it into a Windows app](tensorflow-deploy-model.md).
