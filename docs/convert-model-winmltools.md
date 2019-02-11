---
author: wschin
title: Convert ML models to ONNX with WinMLTools
description: Learn how to use WinMLTools to convert ML models into ONNX format.
ms.author: wechi
ms.date: 11/7/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, WinMLTools, ONNX, ONNXMLTools, scikit-learn, Core ML, Keras
ms.localizationpriority: medium
---

# Convert ML models to ONNX with WinMLTools

[WinMLTools](https://pypi.org/project/winmltools/) enables you to convert ML models created with different training frameworks into ONNX. It is extension of [ONNXMLTools](https://github.com/onnx/onnxmltools) and [TF2ONNX](https://github.com/onnx/tensorflow-onnx) to convert models to ONNX for use with Windows ML. 

WinMLTools currently supports conversion from the following frameworks:

- Apple CoreML
- Keras
- scikit-learn
- lightgbm
- xgboost
- libSVM
- Tensorflow (experimental)


To learn how to export from other ML frameworks, take a look at [ONNX tutorials](https://github.com/onnx/tutorials) on GitHub.

In this article, we demonstrate how to use WinMLTools to:

- Convert CoreML models into ONNX
- Convert scikit-learn models into ONNX
- Convert tensorflow models into ONNX
- Convert onnx models to quantized ONNX models
- Convert floating point models to 16-bit floating point precision models
- Create custom ONNX operators

>[!NOTE]
>The [latest version of WinMLTools](https://pypi.org/project/winmltools/1.3.0/) supports conversion to ONNX versions 1.2.2 and 1.3, as specified respectively by ONNX opsets 7 and 8. Previous versions of the tool do not have support for ONNX 1.3.

## Install WinMLTools

WinMLTools is a Python package (winmltools) that supports Python versions 2.7 and 3.6. If you are working on a data science project, we recommend installing a scientific Python distribution such as Anaconda.

> [!NOTE]
> WinMLTools does not currently support Python 3.7.

WinMLTools follows the [standard python package installation process](https://packaging.python.org/installing/). From your python environment, run:

```console
pip install winmltools
```

WinMLTools has the following dependencies:

- numpy v1.10.0+
- protobuf v.3.6.0+
- onnx v1.3.0+
- onnxmltools v1.3.0+
- tf2onnx v0.3.2+

To update the dependent packages, please run the pip command with ‘-U’ argument.

```console
pip install -U winmltools
```

For different converters, you will have to install different packages for conversion,

For libsvm, You can download libsvm wheel from various web sources. One example can be found here: https://www.lfd.uci.edu/~gohlke/pythonlibs/#libsvm

For coremltools, Currenlty coreml does not distribute coreml packaging on windows. You can install from source:

    pip install git+https://github.com/apple/coremltools


Please follow [onnxmltools](https://github.com/onnx/onnxmltools) on GitHub for further information on onnxmltools dependencies.

Additional details on how to use WinMLTools can be found on the package specific documentation with the help function.

```console
help(winmltools)
```

## Convert CoreML models

Here, we assume that the path of an example Core ML model file is *example.mlmodel*.

~~~python
from coremltools.models.utils import load_spec
# Load model file
model_coreml = load_spec('example.mlmodel')
from winmltools import convert_coreml
# Convert it!
# The automatic code generator (mlgen) uses the name parameter to generate class names.
model_onnx = convert_coreml(model_coreml, 7, name='ExampleModel')   
~~~

> [!NOTE]
>The second parameter in the call to convert_coreml() is the target_opset, and it refers to the version number of the operators in default namespace ai.onnx. See more details on these operators [here](https://github.com/onnx/onnx/blob/master/docs/Operators.md). This parameter is only available on the latest version of WinMLTools, enabling developers to target different ONNX versions (currently 1.2.2 and 1.3 versions are supported). To convert models to run with the Windows 10 October 2018 update, use target_opset 7 (ONNX v1.2.2). For Windows 10 Insider Preview builds greater than 17763, WinML accepts models with target_opset 7 and 8 (ONNX v.1.3). The [Release Notes](release-notes.md) section also contains the min and max ONNX versions supported by WindowsML in different builds.


The `model_onnx` is an ONNX [ModelProto](https://github.com/onnx/onnxmltools/blob/0f453c3f375c1ae928b83a4c7909c82c013a5bff/onnxmltools/proto/onnx-ml.proto#L176) object. We can save it in two different formats.

~~~python
from winmltools.utils import save_model
# Save the produced ONNX model in binary format
save_model(model_onnx, 'example.onnx')
# Save the produced ONNX model in text format
from winmltools.utils import save_text
save_text(model_onnx, 'example.txt')
~~~

> [!NOTE]
>Core MLTools is a Python package provided by Apple, but is not available on Windows. If you need to install the package on Windows, install the package directly from the repo:

```console
pip install git+https://github.com/apple/coremltools
```

## Convert CoreML models with image inputs or outputs

Because of the lack of image types in ONNX, converting Core ML image models (i.e., models using images as inputs or outputs) requires some pre-processing and post-processing steps.

The objective of pre-processing is to make sure the input image is properly formatted as an ONNX tensor. Assume *X* is an image input with shape [C, H, W] in Core ML. In ONNX, the variable *X* would be a floating-point tensor with the same shape and *X*[0, :, :]/*X*[1, :, :]/*X*[2, :, :] stores the image's red/green/blue channel. For gray scale images in Core ML, their format are [1, H, W]-tensors in ONNX because we only have one channel.

If the original Core ML model outputs an image, manually convert ONNX's floating-point output tensors back into images. There are two basic steps. The first step is to truncate values greater than 255 to 255 and change all negative values to 0. The second step is to round all pixel values to integers (by adding 0.5 and then truncating the decimals). The output channel order (e.g., RGB or BGR) is indicated in the Core ML model. To generate proper image output, we may need to transpose or shuffle to recover the desired format.

Here we consider a Core ML model, FNS-Candy, downloaded from [GitHub](https://github.com/likedan/Awesome-CoreML-Models), as a concrete conversion example to demonstrate the difference between ONNX and Core ML formats. Note that all the subsequent commands are executed in a python environment.

First, we load the Core ML model and examine its input and output formats.

~~~python
from coremltools.models.utils import load_spec
model_coreml = load_spec('FNS-Candy.mlmodel')
model_coreml.description # Print the content of Core ML model description
~~~

Screen output:

~~~console
...
input {
    ...
      imageType {
      width: 720
      height: 720
      colorSpace: BGR
    ...
}
...
output {
    ...
      imageType {
      width: 720
      height: 720
      colorSpace: BGR
    ...
}
...
~~~

In this case, both the input and output are 720x720 BGR-image. Our next step is to convert the Core ML model with WinMLTools.

~~~python
# The automatic code generator (mlgen) uses the name parameter to generate class names.
from winmltools import convert_coreml
model_onnx = convert_coreml(model_coreml, 7, name='FNSCandy')    
~~~


An alternative method to view the model input and output formats in ONNX, is to use the following command:

~~~python
model_onnx.graph.input # Print out the ONNX input tensor's information
~~~

Screen output:

~~~console
...
  tensor_type {
    elem_type: FLOAT
    shape {
      dim {
        dim_param: "None"
      }
      dim {
        dim_value: 3
      }
      dim {
        dim_value: 720
      }
      dim {
        dim_value: 720
      }
    }
  }
...
~~~

The produced input (denoted by *X*) in ONNX is a 4-D tensor. The last 3 axes are C-, H-, and W-axes, respectively. The first dimension is "None" which means that this ONNX model can be applied to any number of images. To apply this model to process a batch of 2 images, the first image corresponds to *X*[0, :, :, :] while *X*[1, :, :, :] corresponds to the second image. The blue/green/red channels of the first image are *X*[0, 0, :, :]/*X*[0, 1, :, :]/*X*[0, 2, :, :], and similar for the second image.

~~~python
model_onnx.graph.output # Print out the ONNX output tensor's information
~~~

Screen output:

~~~console
...
  tensor_type {
    elem_type: FLOAT
    shape {
      dim {
        dim_param: "None"
      }
      dim {
        dim_value: 3
      }
      dim {
        dim_value: 720
      }
      dim {
        dim_value: 720
      }
    }
  }
...
~~~

As you can see, the produced format is identical to the original model input format. However, in this case, it's not an image because the pixel values are integers, not floating-point numbers. To convert back to an image, truncate values greater than 255 to 255, change negative values to 0, and round all decimals to integers.

## Convert Scikit-learn models

The following code snippet trains a linear support vector machine in scikit-learn and converts the model into ONNX.

~~~python
# First, we create a toy data set.
# The training matrix X contains three examples, with two features each.
# The label vector, y, stores the labels of all examples.
X = [[0.5, 1.], [-1., -1.5], [0., -2.]]
y = [1, -1, -1]

# Then, we create a linear classifier and train it.
from sklearn.svm import LinearSVC
linear_svc = LinearSVC()
linear_svc.fit(X, y)

# To convert scikit-learn models, we need to specify the input feature's name and type for our converter. 
# The following line means we have a 2-D float feature vector, and its name is "input" in ONNX.
# The automatic code generator (mlgen) uses the name parameter to generate class names.
from winmltools import convert_sklearn
from winmltools.convert.common.data_types import FloatTensorType
linear_svc_onnx = convert_sklearn(linear_svc, 7, name='LinearSVC',
                                  initial_types=[('input', FloatTensorType([1, 2]))])    

# Now, we save the ONNX model into binary format.
from winmltools.utils import save_model
save_model(linear_svc_onnx, 'linear_svc.onnx')

# If you'd like to load an ONNX binary file, our tool can also help.
from winmltools.utils import load_model
linear_svc_onnx = load_model('linear_svc.onnx')

# To see the produced ONNX model, we can print its contents or save it in text format.
print(linear_svc_onnx)
from winmltools.utils import save_text
save_text(linear_svc_onnx, 'linear_svc.txt')

# The conversion of linear regression is very similar. See the example below.
from sklearn.svm import LinearSVR
linear_svr = LinearSVR()
linear_svr.fit(X, y)
linear_svr_onnx = convert_sklearn(linear_svr, 7, name='LinearSVR', 
                                  initial_types=[('input', FloatTensorType([1, 2]))])   
~~~

As before convert_sklearn takes Scikit-learn model as a first argument, and the target_opset for the second argument. Users can replace `LinearSVC` with other scikit-learn models such as `RandomForestClassifier`. Please note that [mlgen](mlgen.md) uses the `name` parameter to generate class names and variables. If `name` is not provided, then a GUID is generated, which will not comply with variable naming conventions for languages like C++/C#.

## Convert Scikit-learn pipelines

Next, we show how scikit-learn pipelines can be converted into ONNX.

~~~python
# First, we create a toy data set.
# Notice that the first example's last feature value, 300, is large.
X = [[0.5, 1., 300.], [-1., -1.5, -4.], [0., -2., -1.]]
y = [1, -1, -1]

# Then, we declare a linear classifier.
from sklearn.svm import LinearSVC
linear_svc = LinearSVC()

# One common trick to improve a linear model's performance is to normalize the input data.
from sklearn.preprocessing import Normalizer
normalizer = Normalizer()

# Here, we compose our scikit-learn pipeline. 
# First, we apply our normalization. 
# Then we feed the normalized data into the linear model.
from sklearn.pipeline import make_pipeline
pipeline = make_pipeline(normalizer, linear_svc)
pipeline.fit(X, y)

# Now, we convert the scikit-learn pipeline into ONNX format. 
# Compared to the previous example, notice that the specified feature dimension becomes 3.
# The automatic code generator (mlgen) uses the name parameter to generate class names.
from winmltools import convert_sklearn
from winmltools.convert.common.data_types import FloatTensorType, Int64TensorType
pipeline_onnx = convert_sklearn(linear_svc, name='NormalizerLinearSVC',
                                input_features=[('input', FloatTensorType([1, 3]))])   

# We can print the fresh ONNX model.
print(pipeline_onnx)

# We can also save the ONNX model into binary file for later use.
from winmltools.utils import save_model
save_model(pipeline_onnx, 'pipeline.onnx')

# Our conversion framework provides limited support of heterogeneous feature values.
# We cannot have numerical types and string type in one feature vector. 
# Let's assume that the first two features are floats and the last feature is integer (encoded a categorical attribute).
X_heter = [[0.5, 1., 300], [-1., -1.5, 400], [0., -2., 100]]

# One popular way to represent categorical is one-hot encoding.
from sklearn.preprocessing import OneHotEncoder
one_hot_encoder = OneHotEncoder(categorical_features=[2])

# Let's initialize a classifier. 
# It will be right after the one-hot encoder in our pipeline.
linear_svc = LinearSVC()

# Then, we form a two-stage pipeline.
another_pipeline = make_pipeline(one_hot_encoder, linear_svc)
another_pipeline.fit(X_heter, y)

# Now, we convert, save, and load the converted model. 
# For heterogeneous feature vectors, we need to seperately specify their types for all homogeneous segments.
# The automatic code generator (mlgen) uses the name parameter to generate class names.
another_pipeline_onnx = convert_sklearn(another_pipeline, name='OneHotLinearSVC',
                                        input_features=[('input', FloatTensorType([1, 2])),
                                        ('another_input', Int64TensorType([1, 1]))])
save_model(another_pipeline_onnx, 'another_pipeline.onnx')
from winmltools.utils import load_model
loaded_onnx_model = load_model('another_pipeline.onnx')

# Of course, we can print the ONNX model to see if anything went wrong.
print(another_pipeline_onnx)
~~~

## Convert Tensorflow models

The following code is an example of how to convert a model from a frozen tensorflow model. To get possible output names of a tensorflow model, you can use [summarize_graph tool](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/tools/graph_transforms)

~~~python
import winmltools
import tensorflow

filename = 'frozen-model.pb'
output_names = ['output:0']

graph_def = graph_pb2.GraphDef()
with open(filename, 'rb') as file:
  graph_def.ParseFromString(file.read())
g = tf.import_graph_def(graph_def, name='')

with tf.Session(graph=g) as sess:
  converted_model = winmltools.convert_tensorflow(sess.graph, 7, output_names=['output:0'])
  winmltools.save_model(converted_model)
~~~

WinMLTools converter uses the tf2onnx.tfonnx.process_tf_graph in [TF2ONNX](https://github.com/onnx/tensorflow-onnx). 

## Convert to floating point 16

WinMLTools supports the conversion of models represented in floating point 32 into a floating point 16 representation, effectively compression the model by reducing its size in half. 

> [!NOTE]
> Quantization could result in loss of accuracy in the resulting model. Make sure you verify the model's accuracy before deploying into your application.

Below is a full example if you want to convert directly from an ONNX binary file. 

~~~python
from winmltools.utils import convert_float_to_float16
from winmltools.utils import load_model, save_model
onnx_model = load_model('model.onnx')
new_onnx_model = convert_float_to_float16(onnx_model)
save_model(new_onnx_model, 'model_fp16.onnx')
~~~

Parameters:
per_channel: If set to True, the quantizer will linearly dequantize for each channel in initialized tensors for Conv operators in [n,c,h,w] format. By default this is set to True.

With `help(winmltools.utils.convert_float_to_float16)`, you can find more details about this tool. The floating data 16 in WinMLTools currently only complies with [IEEE 754 floating point standard (2008)](https://en.wikipedia.org/wiki/Half-precision_floating-point_format).

>[!NOTE]
>Reducing the model size may result in accuracy loss. We recommend you always check the resulting model's accuracy before deploying to your application.

## Quantize ONNX model

WinMLTools also supports compressing existing ONNX models by using the quantize operator. The tool currently supports linear quantization from 32 bit floating point data into 8 bit data. 

> [!NOTE]
>Quantization could result in loss of accuracy in the resulting model. Make sure you verify the model's accuracy before deploying into your application.

Below is a full example if you want to convert directly from an ONNX binary file. 
~~~python
import winmltools

model = winmltools.load_model('model.onnx')
quantized_model = winmltools.quantize(model, per_channel=True, nbits=8, use_dequantize_linear=True)
winmltools.save_model(quantized_model, 'quantized.onnx')

~~~
Input parameters definition:

- `per_channel`: If set to True, the quantizer will linearly dequantize for each channel in each initialized tensors in [n,c,h,w] format. By default this parameter is set to True.
- `nbits`: number of bits to represent quantized values. Currently only 8 bits is supported. 
- `use_dequantize_linear`: If set to True, the quantizer will linearly dequantize for each channel in initialized tensors for Conv operators in [n,c,h,w] format. By default this is set to True.


## Create custom ONNX operators

When converting from a Keras or a Core ML model, you can write a custom operator function to embed custom [operators](https://github.com/onnx/onnx/blob/master/docs/Operators.md) into the ONNX graph. During the conversion, the converter invokes your function to translate the Keras layer or the Core ML LayerParameter to an ONNX operator, and then it connects the operator node into the whole graph.

1. Create the custom function for the ONNX sub-graph building.
2. Call `winmltools.convert_keras` or `winmltools.convert_coreml` with the map of the custom layer name to the custom function.S
3. If applicable, implement the custom layer for the inference runtime.  

The following example shows how it works in Keras.

~~~python
# Define the activation layer.
class ParametricSoftplus(keras.layers.Layer):
    def __init__(self, alpha, beta, **kwargs):
    ...
    ...
    ...

# Create the convert function.
def convert_userPSoftplusLayer(scope, operator, container):
      return container.add_node('ParametricSoftplus', operator.input_full_names, operator.output_full_names,
        op_version=1, alpha=operator.original_operator.alpha, beta=operator.original_operator.beta)

winmltools.convert_keras(keras_model, 7,
    custom_conversion_functions={ParametricSoftplus: convert_userPSoftplusLayer })
~~~

[!INCLUDE [help](includes/get-help.md)]
