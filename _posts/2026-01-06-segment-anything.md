---
title: 'Segment Anything'
date: 2026-01-06
permalink: /posts/2026/01/segment-anything/
excerpt: 'SAM (Segment Anything Model) is a foundation model for promptable image segmentation — using a ViT encoder pretrained with MAE, a prompt encoder for points/boxes/masks, and an ambiguity-aware decoder that outputs multiple candidate masks, trained on 1.1B masks for zero-shot transfer.'
tags:
  - Computer Vision
  - Segmentation
  - Foundation Models
  - Vision Transformer
---

## Overview

The authors wanted to build a foundation model, by designing prompts that can handle ambiguity. The model must return at least one mask.

## Dataset

**Scale:** 1.1B masks; incrementally generated this data. During the fully automated stage, they divide the image into a grid and generate bounding boxes. This could typically lead to multiple bounding boxes, however they use Non-Maximal Suppression to keep only one of those bounding boxes. (You can imagine that different grid resolutions can be used for such a prediction)

## Architecture

Let's say we provide some point input to the model for guiding segmentation, this would generate a mask which need not be perfect. Then for subsequent iterations, that mask is provided as the input at the mask position into the convolution network.

### Image Encoder

**Standard ViT pre-trained on MAE objective.** It takes in large resolution images and generates embeddings for them.

**Vision Transformer:** Output is a representation corresponding to each patch, as well as output corresponding to a CLS token. This CLS token is attached to a MLP, why? To transform from the image representation space to the classifier label space.

**Masked Autoencoder Vision Transformer:** After encoding the unmasked image patches, they are arranged as per their initial order and the goal is to regenerate the missing image patches. This means that the learned image patches have to be representative of the entire image.

### Prompt Encoder

The prompt encoder can consider two kinds of inputs one is sparse and the other is dense. The sparse embeddings can be additive points (x, y) or foreground points and the reductive points are also called background points. The aim is to represent these points with a 256 dimensional size so as to match the embedding size of the vision transformer. Similarly the bounding boxes are represented by a set of two points.

These (x, y) coordinates have to be represented in a form that the transformer model can digest. As we have seen with language transformer models that sinusoidal embeddings are fit for this task. However, the additional challenge here is that these are two points, and one natural solution is to use separate sinusoidal representations. However, there is an inherent issue with this as shown in the image below.

If we look at the pointwise similarity, the second image shows that similarity reduces with euclidean distance.

Finally, the dense representation which is a mask, can simply be added pointwise to the underlying image.

### Decoder

The decoder has a lot of moving parts. The overarching goal corresponds to learning 3 masks and 1 representation corresponding to the image. One additional goal is to predict the IoU score. Ultimately, an image representation (token conditioned image representation) is used to predict the IoU scores and the piecewise (dot) product of the token (mask) representations and the image embeddings are used to generate the masks. Moreover, they keep adding the positional embeddings so as to not lose the edge information.

**Ambiguity Aware predictions:** Why are the 3 masks being predicted? Because for only one prompt as input, the model generates 3 potential masks. However, if two prompts are provided, the model produces just the singular output mask, hence the model is ambiguity aware. Hence, this 4th token is turned only for more than 2 prompts.

### Loss

**Focal and dice loss:** Since most of the pixels are not marked during the prompting phase, this represents a class imbalance problem, however cross-entropy loss upon which focal loss is based does not take this class imbalance into consideration. Hence, we have to create focal loss. On the other hand, dice loss is given by:

$$\text{Dice Loss} = 1 - \frac{2|X \cap Y|}{|X| + |Y|}$$

## Strengths

- **Promptable segmentation task:** Zero-shot transfer using prompts (bounding boxes, points or text)
- **Reduced annotation cost:** Previous segmentation models have a pixel-wise annotation cost
- **Encoder-Decoder model:** Model runs in real-time in browser

## Weaknesses

- Complicated architecture when it comes to using one point vs two points for changing the segmentation mask

## Rating

**5/5** - This is a foundation model in CV, the authors innovate in everything from data labeling to inference cost.
