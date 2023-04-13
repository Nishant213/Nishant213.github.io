---
title: "Convoluted Neural Networks"
date: 2021-05-09T19:15:40+05:30
author: "NishantN"
env: 'production'
draft: false
showToc: true
disableHLJS: false
ShowShareButtons: false
ShowBreadCrumbs: false
ShowPostNavLinks: false
tags: ['computer vision']
unsafe: true
---
## Introduction
This is a blog about convoluted neural networks that explains what they are and how they operate. Knowledge about neural networks will help in understanding this blog better. I you are not sure what neural networks are, you can head over and read my blog on neural networks [here](https://www.nishantnadkarni.tech/posts/neural-networks/).
## About Images
Before diving into CNNs, let's pause and understand how an image is represented first. An image is essential a matrix of pixels. As you can see in the image, greyscale image are a simple n\*n matrix with 255 being the pixel value for lightest shade and 0 being the value for the darkest shade. Higher the pixel values, darker is the pixel and vice versa. Colored images are a 3\*n\*n matrix i.e. three n\*n matrices of Red, Green and Blue colors respectively (in that order) called RGB. With mixing different pixel values of these colors, we can make any color we want.

<center>
<img src="/cnn/pixels.png" align="center">
</center>

## What are Convoluted Neural Networks?
Convoluted Neural Networks (CNN) are a type of neural networks which has one or more convoluted layers. A convolution is essentially sliding a filter over the input. We will talk about convolutions and convoluted layers in more detail in a moment. Standard neural networks are less efficient while dealing with images as it would take a lot of them to process an image properly. Hence mainly while dealing with image data, we use convoluted neural networks.

![Convolutional Neural Network](/cnn/cnn.png)
<center>Architecture of a typical CNN</center>

## Types of Layers
Traditionally convoluted neural networks consist of 3 layer:
1. Convoluted Layers
2. Pooling Layers
3. Fully Connected Layers



### Convoluted Layers
Convolutional layers convolve the input and pass its result to the next layer. This is done by the use of sliding filters or kernels which map on top of the image. The filter is a matrix of integers that are used on a subset of the image’s pixel values. Each pixel (image) is multiplied by the corresponding value in the kernel, then the result is summed up for a single value for simplicity representing a grid cell, like a pixel, in the output channel/feature map.

<center>
<img src="/cnn/conv1.gif" align="center">
</center>
<br>
<center>Kerel performing convolution on an image</center>

### Pooling Layers
Pooling layers are used to reduce the dimension of the convoluted feature map extracted from convoluted layers. Pooling layers reduce the computational power needed to process the data while summarizing the prominent features of the feature map. Like convolutional layers, this is also done with the use of filters.

There are two types of pooling layers:
1. Max Pooling - The max value of the pixels in the area of the filter is extracted
2. Average Pooling - The average value of all pixels in the area of the filter is extracted

![Pooling Layer](/cnn/pool.gif)
<center>Types and working of pooling layers</center>

### Fully Connected Layers
They are also called linear of dense layers. These are basically the same as basic artificial neural networks. It's input is the flattened output of pooling or convoluted layers. Flattening is basically converting a two or three dimensional matrix (feature map) into a one dimensional vector. Since the working of this layer has been extensively covered in my blog on ANNs, I will not cover it again here. You can check out that blog [here](https://www.nishantnadkarni.tech/posts/neural-networks/).

![Flatten](/cnn/flatten.png)
<center>Flattening a 2D Feature Map</center>