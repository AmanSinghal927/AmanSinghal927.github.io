---
title: 'ZeRO: Memory Optimizations Toward Training Trillion Parameter Models'
date: 2026-01-06
permalink: /posts/2026/01/zero-memory-optimizations/
excerpt: 'Training large models requires far more memory than just storing weights — a 1.5B parameter model needs ~3GB for weights but ~24GB total due to Adam optimizer states (momentum, variance) and gradients. ZeRO (Zero Redundancy Optimizer) solves this by partitioning optimizer states, gradients, and parameters across GPUs — eliminating redundant storage while maintaining data parallelism, unlike traditional approaches that either replicate everything (DP) or suffer from low utilization (MP/PP). The three ZeRO stages progressively reduce memory: ZeRO-1 partitions optimizer states, ZeRO-2 adds gradient partitioning, and ZeRO-3 partitions parameters too, with communication overhead comparable to standard data parallelism.'
tags:
  - Distributed Training
  - Memory Optimization
  - Deep Learning Systems
  - Adam Optimizer
---

## Case: Adam Optimizer

A 1.5B parameter model in 16-bit precision needs only ~3GB worth of storage for model weights. However, it is the practical limit for a 32GB GPU, why?

This is because of optimizer states, namely a copy of the model weights, the momentum and rmsprop in the case of Adam. Adam makes weight updates by:

$$w = w - g \cdot \frac{m}{\sqrt{v + \epsilon}}$$

Thus, in mixed (16-bit and 32-bit) precision it needs 32-bits worth of representation for each of these, which comes down to $4\Psi + 4\Psi + 4\Psi$. Moreover, for the forward and backward pass Adam needs a 16-bit representation of the weights and the gradients. Thus, if K represents the memory multiplier of the optimizer, the memory requirement is $2\Psi + 2\Psi + K\Psi$; **K = 12 for Adam**.

## Historical Approaches to Scaling

Now, historically, there are 3-ways of handling something like this:
1. **Data Parallelism (DP)**
2. **Model Parallelism (MP)**
3. **Pipeline Parallelism (PP)**

### Data Parallelism (DP)

DP replicates the model across GPUs, splitting on data, hence the problem becomes that a single model should fit the single GPU.

### Model Parallelism (MP)

MP splits the model vertically and disperses its layers across GPUs, however in this scenario the inter-gpu communication or the memory bandwidth of a cluster of GPUs becomes the bottleneck and GPUs would end up having fairly low utilization.

### Pipeline Parallelism (PP)

PP is a neat trick, where as the first model layer finishes its processing, it is fed with another data input, making sure that each of the GPUs are kept busy. However, examples of PP such as PipeDream does this by maintaining data redundancy. Hence, we are inspired to use **ZeRO - which stands for Zero Redundancy**.

## ZeRO Optimizations

Let's have a look at the kind of optimizations that ZERO makes:

### 1. Optimizer State Partitioning (ZeRO-1)

All the optimizer states for the entire model are split, and each GPU is responsible only for updating those optimizer states. We must remember that this is essentially done in a data parallel way. This means that only some of the data is responsible for updating only some of the optimizer states.

These optimizer states are like momentum, which are calculated as:

$$m = (1-\beta) \cdot m + \beta \cdot g$$

### 2. Gradient Partitioning (ZeRO-2)

We need to remember that even though optimizer states like momentum can be easily updated in a segmented fashion, gradients for each data point must be calculated starting from the last layer. Hence, for managing gradients with DP, unnecessary gradients are dropped as and when each segment makes updates using them.

### 3. Parameter Partitioning (ZeRO-3)

Similar to gradients, the parameters are only stored for specific segments, however in this scenario, each segment may require communication with other segments. This communication overhead comparable to DP ($2\Psi$) which is around $3\Psi$.

## Strengths

The routine enables, with minimal intervention to store and work with large models using multiple GPUs.

## Weaknesses

One problematic setting is where only a single GPU is available.

## Rating

**4/5** - The paper is fairly accessible for implementation through libraries such as DeepSpeed that are well-integrated with distributions of transformers like Hugging Face.
