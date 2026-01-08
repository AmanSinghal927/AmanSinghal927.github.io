---
title: 'DINOv2: Learning Robust Visual Features without Supervision'
date: 2026-01-06
permalink: /posts/2026/01/dinov2-visual-features/
excerpt: 'DINOv2 is a self-supervised foundation model for computer vision that trains a 1B parameter ViT on 142M curated images using dual objectives: an image-level cross-entropy loss between a student (trained on crops) and an EMA-updated teacher (trained on full images), plus a patch-level masked prediction task with decoupled weights. Key innovations include scalable dataset curation — using clustering-based deduplication and similarity-based augmentation with FAISS indexing instead of manual annotation — achieving 2x faster training and 3x less memory than iBOT while learning transferable features at both image and patch levels.'
tags:
  - Computer Vision
  - Foundation Models
  - Self-Supervised Learning
  - Vision Transformer
---

## Overview

- **Foundational model** for computer vision
- **Curated data:** Cleaning is done
- **Aim:** Stability at scale
- **ViT:** Train ViT with 1B parameters
- **Distill** smaller models to mimic the larger model
- They're saying there are some limits to text and image pre-training
- They are just training on images for pixel and image level tasks

**Key Insight:** PCA on the image encoder features shows that the head of the elephant is the same across poses. Moreover, it learns similar features for parts of 1 horse and parts of multiple horses.

## Task

**Self-supervised learning** (previously done on imagenet 1K); features are learned at both **image and patch level**.

- **2x faster** and requires **3x less memory** as compared to iBOT, which mentions discriminative approaches to self-supervised learning
- **Learning on larger batch sizes:** Don't decrease the learning rate, increase the batch size, because larger batch sizes lead to more stable gradients - because larger batch sizes are more representative of the population

## Dataset Curation

- Instead of manual annotation, data curation is done by using **clustering based approaches** for similarity
- Moreover, it is important to **rebalance** this data since otherwise it might lead to a few dominant modes in the data
- End up with a dataset of size **142M images**
- Learn frozen and transferable features
- Use similarity between curated and uncurated images to augment the curated images using embeddings

### Data Processing Steps

**Deduplication:** Removing images with very similar images.

**Data Augmentation:** To augment the dataset they divide their curated data in query datasets, for example all dogs and they also cluster all the uncurated datasets. Now based on the criteria of cosine similarity they sample top N (=4) images from clusters. However, in cases where the number of images in the query dataset is small, they sample M images instead.

**Indexing:** Use the FAISS db for indexing and retrieval.

## Training Objective

### Image-level Objective

**Cross Entropy between teacher and student models:** The student model is trained on a cropped version of an image which the teacher is trained on the entire image, the goal is to minimize the cross-entropy loss between the teacher and the student model. Now, if we keep the teacher model stagnant, over-time the student will memorize the teacher's responses. Hence, it is necessary to update the teacher model. This is done through an **exponential moving average (EMA)**:

$$\text{teacher} = \beta \times \text{teacher} + (1-\beta) \times \text{student}$$

### Patch-level Objective

Mask some of the patches for the student.

### Untying Weights

The authors note that coupling weights from the image and patch level objectives leads to underfitting at the patch level and overfitting at the image level, hence it is best to **decouple these weights**.

### Sinkhorn-Knopp Algorithm

Use softmax for normalizing the student network and Sinkhorn-Knopp for the teacher network.

## Strengths

Do not need any supervision, hence the representation learned is so optimal that the model can perform well on a variety of tasks.

## Weaknesses

The training objectives are multidimensional and complex.

## Rating

**5/5** - This is a foundational model for Computer Vision, the paper thoroughly discusses the dataset collection process, SSL objectives etc.
