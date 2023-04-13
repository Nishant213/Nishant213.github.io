---
title: "RSVQA: Visual Question Answering for Remote Sensing Data"
date: 2021-11-03T04:58:55+05:30
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

This blog is about the work done by Sylvain Lobry, Diego Marcos, Jesse Murray and Devis Tuia in their paper 'RSVQA: Visual Question Answering for Remote Sensing Data'. This paper was published in IEEE Transactions on Geoscience and Remote Sensing (2020).
You can check out the published paper [here](https://ieeexplore.ieee.org/document/9088993) or check out the pre-acceptance version [here](https://arxiv.org/pdf/2003.07333.pdf).

## What is Visual Question Answering?

Visual Question Answering (VQA) is a research area about making computer systems answer questions based on images. One of the most famous datasets in this domain is the VQA dataset which is compiled by Virginia Tech and Georgia Tech. An example from the dataset can be seen below.

![VQA Example](/rsvqa/vqa.jpg)
You can check out the dataset [here](https://visualqa.org/)

As you can imagine making accuracy VQA models would significantly improve the possibilities in human machine interactions allowing us to dynamically extract information needed from images. A VQA model usually consists of four distinctive components:
1. Visual feature extractor 
2. Language feature extractor 
3. Fusion step between the two modalities
4. Prediction component

We shall speak in detail about the functioning of these components in this paper further in this article.

## What is Remote Sensing Data?

Remote sensing is the process of detecting and monitoring the physical characteristics of an area by measuring its reflected and emitted radiation at a distance (typically from satellite or aircraft). Data collected from this process is called remote sensing data. In this paper, they have made two such datasets (high and low resolution). I shall speak more on these datasets further in this article.

<center>
<img src="/rsvqa/remote.png" align="center">
Example of a remote sensing image
</center>

## Visual Question Answering for Remote Sensing Data

Visual Question Answering for Remote Sensing Data (RSVQA) is a proposed system to extract information from remote sensing data that is accessible to every user. With this system, images can be queried to obtain high-level information specific to the image content or relational dependencies between objects visible in the images. As mentioned above, this paper also includes creation of two datasets for this task which are used to train and evaluate supervised learning models to solve this task.

### Datasets

Since VQA is a relatively new field, the main limitating factor for VQA is the availability of task specific datasets. To tackle this problem, the authors of this paper have made their own datasets for this task. A VQA dataset typically consists of images with multiple questions and answers associated with them. The RSVQA dataset is no different. They have OpenStreetMap (OSM) data and used the geolocalized information provided to extract the information required to make the question/answer pairs.

The dataset is divided into two types:
1. LR 
- Based on nine Sentinel-2 tiles covering The Netherlands with low cloud cover. 
- Divided into 772 images of 256x256 each.
- 77,232 questions were generated based on these images. 
- Split into 77.8% training set, 11.1% validation set and 11.1% test set
<center>
<img src="/rsvqa/lr.png" align="center">
Example of Low Resolution Data
</center>

2. HR
- Extracted from the high-resolution orthoimagery (HRO) data collection of the USGS over the North Eastern part of the USA.
- Covers mainly Urban areas of the USA
- Consists of 10,659 images of size 512x512 each
- 10,66,316 questions were generated based on these images. 
- Split into 61.5% training set, 11.2% validation set, 20.5% test set 1 and 6.8% test set 2.
- Training and validation sets contain images from New York City, Long island and Portland whereas test sets contain images from New York City, Long Island and Philadelphia. 
<center>
<img src="/rsvqa/HR.png" align="center">
Examples of High Resolution Data
</center>

<br>
Both these datasets have several types of questions which you can see in the diagram below:

![Questions Distribution](/rsvqa/ans_dist.png)

### Model

The model used in this paper is a fairly basic VQA model. It is composed of three parts:
#### A. Feature Extraction 

This part is responsible for extracting a feature vector from the inputs. It has two parts:

*Visual Part* - This part is responsible for extracting the features of the images. For this, the authors use a ResNet 152 pretrained on ImageNet as it avoids the degradation problem by the use of residual mappings of layers. The last fully connected and average pooling layers are replaced with a 1x1 Conv2D layer that outputs 2048 features. These features are then passed through a fully connected layer to get a feature vector of 1200 elements.

*Language Part* - This is responsible for processing the questions. This uses skip-thoughts networks pretrained on the BookCorpus dataset to get the feature embeddings. This is a recurrent neural network that works to predict sentences (words in case of questions) before and after any given sentence to better understand the current sentence. This model takes the tozenized version of questions as input and outputs a vector of 2400 elements which is reduced to 1200 elements using a fully connected layer.

#### B. Fusion
This step is responsible for merging the two feature vector into one in order to make predictions using both the image and the question. If you noticed both the image and text features are of the same size (1200 elements). These vectore are now activated using a hyperbolic tangent function before combining them using fixed (no learnable parameters) pointwise multiplication. Keeping this function fixed encourages both  feature vectors to be comparable with respect to this operation.

#### C. Prediction
Post fusion, we get a vector of 1200 elements which is now reduced to the output number of answers using a multilayer perceptron with one hidden layer of 256 elements.

![VQA Model](/rsvqa/vqamodel.png)
<center>Model Architecture</center> 

### References
S. Lobry, D. Marcos, J. Murray and D. Tuia, "RSVQA: Visual Question Answering for Remote Sensing Data," in IEEE Transactions on Geoscience and Remote Sensing, vol. 58, no. 12, pp. 8555-8566, Dec. 2020, doi: 10.1109/TGRS.2020.2988782.