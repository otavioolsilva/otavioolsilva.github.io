---
title: "Extra research checkpoint #01: Using the Raspberry Pi to recognize objects"
date: 2025-01-24 09:30:00 -0300
categories: [Undergraduate Research Project]
tags: [computer-networks, security, IoT, ML]
image:
  path: /assets/img/2025-01-24-extra-research-01/rasp-inference.jpg
  alt: Raspberry Pi recognizing a coffee mug with 97% confidence.
---

In the work plan for this research project, one of the defined focuses of study was neural network models, chosen to perform the inference on network traffic in order to detect anomalies in it and, consequently, DDoS (Distributed Denial of Service) attacks, which are our objective. To justify this choice, we stated that this category of models already have demonstrated a good performance on low capacity devices. A Raspberry Pi is a good example of this kind of device and there are known cases of neural networks performing well on this small computer, as exemplified in this [PyTorch tutorial](https://pytorch.org/tutorials/intermediate/realtime_rpi.html). This publication was cited in our work plan and, before the beginning of the project, I decided to follow its steps and reproduce the test. It has been some time since I first did this experiment, but I decided to replicate it again and report it here, in the context of our [recent tests with the Raspberry](https://otavioolsilva.github.io/posts/research-10/).

The idea of that post is to demonstrate how PyTorch, a Python machine learning library, has out-of-the-box support for Raspberries. The tutorial was written using a [Raspberry Pi 4 Model B 4GB](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) as reference, which has technical specifications better than the one I used here, a [Raspberry Pi 3 Model B](https://www.raspberrypi.com/products/raspberry-pi-3-model-b/), one generation older than the reference device. But it still presented an interesting performance, considering its specifications:

- Quad Core 1.2GHz Broadcom BCM2837 64bit CPU
- 1GB RAM
- Debian GNU/Linux 12 (bookworm) OS
- 100 Mbits/sec network interface card (Fast Ethernet)

Together with the device, I also used a camera module with a sensor of 5MP. It's capable of taking photos with a resolution up to 2592x1944 pixels and to record videos at 1080p and 30 fps, reaching up to 90 frames per second in lower resolutions. The connection is made through a Camera Serial Interface (CSI).

Together with PyTorch, the tutorial instructs to use the [OpenCV](https://opencv.org/) library to capture the frames and the pre-trained [MobileNetV2](https://pytorch.org/vision/main/models/mobilenetv2.html) model to perform the inference. This ML model is a convolutional neural network optimized to perform well in mobile devices and trained over the [ImageNet](https://www.image-net.org/) image database. In this way, the model is capable of performing inferences over images with 224x224 pixels identifying, for example, common house objects.

The Python script used is available [here](https://github.com/otavioolsilva/ddos-detection-iot-SI/blob/main/extra/rasp-inference.py). It's almost the same one as the original in the post, just with a difference: the inference returns an identifier and not the actual description of the image, the script in the post uses a dictionary named `classes` to do this conversion, but I don't have access to it. In order to resolve this problem, I used a dictionary available in this [GitHub repository](https://github.com/nottombrown/imagenet-stubs/blob/master/imagenet_stubs/imagenet_2012_labels.py), which contains a list of labels assigned to ImageNet image IDs. The versions of the Python libraries used are:

- `torch`: 2.3.1
- `torchaudio`: 2.3.1
- `torchvision`: 0.18.1
- `opencv-python`: 4.10.0.84
- `numpy`: 2.0.0

During its execution, the model was able to recognize different kinds of objects with a high confidence, such as a coffee mug, a cup, a plant pot (vase), a t-shirt, a running shoes... The performance observed is lower than the one with the Raspberry Pi 4 in the original post, but still decent in my opinion: during one minute of execution with a coffee mug in front of the camera, the script reported an average of approximately 17.89 fps, with all frame rates in the range [15.68, 18.06].

The objective with this post was to document this experiment and use it to demonstrate that the Raspberry Pi really performed reasonably dealing with a neural network model, as we stated in our work plan. When it comes to IoT (Internet of Things) devices, they can be even more limited than the one I used here and this is a concern that we have to keep in mind when developing applications that will run on these platforms. Especially in our context of studies in cyberattack detection, the solutions developed are expected to run in the background, with low resources consumption. Therefore, neural networks seem to be an interesting choice, which will require further testing, as we started in [post 06](https://otavioolsilva.github.io/posts/research-06/). 

> This post was made as a record of the progress of the research project "DDoS Detection in the Internet of Things using Machine Learning Methods with Retraining", supervised by professor [Daniel Batista](https://www.ime.usp.br/~batista/), BCC - IME - USP. Project supported by the São Paulo Research Foundation (FAPESP), process nº 2024/10240-3. The opinions, hypothesis and conclusions or recommendations expressed in this material are those of the author and do not necessarily reflect the views of FAPESP.
{: .prompt-info }

