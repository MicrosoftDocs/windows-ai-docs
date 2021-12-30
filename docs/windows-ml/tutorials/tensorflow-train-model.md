---
title: Train your ML model with TensorFlow
description: Learn how to use TensorFlower to configure your Windows ML model.
ms.date: 5/8/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials, pytorch
---

# Train an object detection model with TensorFlow

Now that we've configured TensorFlow, we'll use the YOLO architecture to train the object detection model. YOLO is a neural network which predicts bounding boxes and class probabilities from an image in a single evaluation. YOLO models can process over 60 frames per second, making it a great architecture for detecting objects in videos. You can find more information on how YOLO works [here](https://arxiv.org/pdf/1506.02640v5.pdf).

## Using YOLO

First, download [this YOLO sample file](https://github.com/microsoft/DirectML/tree/master/TensorFlow/yolov3), which contains helper scripts to get started.

When using YOLO, we have 3 options:

1.	**Utilize pre-trained model weights for YOLO.** The pre-trained model has been trained on a large dataset with 80 classes (categories) for everyday objects like bus, person, sandwich, etc. If you want to download a pre-trained YOLO model in ONNX format, you can do so [here](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/Tutorial%20Samples/YOLOv4ObjectDetection). Then, you can proceed to [the final stage of this tutorial](tensorflow-deploy-model.md) to learn how to integrate that model into an app.

2.	**Implement transfer learning with a custom dataset.** Transfer learning is a method for using a trained model as a starting point to train a model solving a different but related task. This tutorial will use the pre-trained YOLO weights with 80 classes to train a model with 20 classes with the [VOC dataset](https://www.tensorflow.org/datasets/catalog/voc). If you want to create your own dataset with custom classes, see instructions [here](https://github.com/AlexeyAB/Yolo_mark).

3.	**Train YOLO from scratch.** This technique is not recommended, because it is very difficult to converge. The original YOLO paper trained darknet on imagenet (containing hundreds of thousands of photos) before training the whole network as well.

## Implement transfer learning on pre-trained YOLO weights to the VOC dataset:

Let's proceed with the second option, and implement transfer learning with the following steps.

1. In a miniconda window, navigate to the yolo sample directory and run the following command to install all the required pip packages for YOLO.

`pip install -r requirements.txt`

2. Run the setup script to download the data and pre-trained weights

`python setup.py`

3. Transform the dataset. See `tools/voc2012.py` for implementation - this format is based on the [tensorflow object detection API](https://github.com/tensorflow/models/tree/master/research/object_detection). Many fields are not required, but have here been filled in for compatibility with the official API.

```
python tools/voc2012.py \
  --data_dir './data/voc2012_raw/VOCdevkit/VOC2012' \
  --split train \
  --output_file ./data/voc2012_train.tfrecord

python tools/voc2012.py \
  --data_dir './data/voc2012_raw/VOCdevkit/VOC2012' \
  --split val \
  --output_file ./data/voc2012_val.tfrecord
```

4. Train the model. Run the following commands:

```
python convert.py
python detect.py --image ./data/meme.jpg # Sanity check

python train.py \
	--dataset ./data/voc2012_train.tfrecord \
	--val_dataset ./data/voc2012_val.tfrecord \
	--classes ./data/voc2012.names \
	--num_classes 20 \
	--mode fit --transfer darknet \
	--batch_size 16 \
	--epochs 10 \
	--weights ./checkpoints/yolov3.tf \
	--weights_num_classes 80 
```

You now have a re-trained model with 20 classes, ready for use.

## Next steps

Now that we've created a TensorFlow model, we need to [convert it to ONNX format](tensorflow-convert-model.md) for use with the Windows Machine Learning APIs.
