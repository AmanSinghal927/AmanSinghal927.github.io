---
title: 'FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning'
date: 2026-01-06
permalink: /posts/2026/01/flashattention-2/
tags:
  - Attention Mechanisms
  - GPU Optimization
  - Deep Learning Systems
  - Memory Efficiency
---

## GPU Execution Flow

We start by looking at the GPU execution flow. The GPU is made up of:
- **SMs (Streaming Multiprocessors)**
- **HBM (High Bandwidth Memory)** - Large (40-80 GB) but low memory bandwidth (1.5-2 TB/s)
- **On-chip SRAM (Shared Memory)** - Small (192KB) but high memory bandwidth (19-20 TB/s)

## GPU Thread Architecture

**What are threads (aka kernels)?** Threads or kernels are organized into thread blocks and are scheduled to run on SMs. Within each thread block, threads are organized into **warps**. Warps are made for executing the same set of operations on different data pieces.

### CPU vs GPU

If we look at what is the difference between a CPU and a GPU, we realize that CPUs work on sequential tasks, and create the illusion of parallel operation. If we talk about how GPUs and CPUs collaborate to render scenes in a video game: The CPU provides game logic and points to the GPU, then by a process called rasterization and several other real-like image optimizations for each pixel, the GPU produces realistic graphics. This emphasized that **GPUs are optimized for running scenes where the same set of operations are to be performed on different data points** (e.g. pixels).

Now, throughout this endeavor different components of the GPU interact with each other (which is called the memory bandwidth). However, for communication between two warps a shared underlying memory space is used and hence this communication is not expensive. Similarly, different threads can also easily communicate with each other.

## FlashAttention (Original)

Now, coming to the FlashAttention paper (not the FlashAttention 2 paper), there are 2 major optimizations that are made:

### 1. Tiling

Here a block of memory is read from the HBM and then operations are performed on it.

### 2. Reducing HBM Reads/Writes

Reducing the number of reads and writes from and to HBM. To execute this for attention, a key or a query matrix is divided into several parts (say 2). This has several advantages:

- **Optimized memory bandwidth:** We can transfer smaller blocks and hence a single large block does not waste the GPU's power of parallel processing
- **Reduced communication:** Since there are a smaller number of blocks each can be worked upon individually, reducing the communication between different thread blocks
- **Reduced I/O:** Operations of writing to and from the HBM can be reduced as intermediate steps can be stored in SRAM

## FlashAttention-2 Optimizations

Moving from FlashAttention to FlashAttention-2, there are several major optimizations:

### 1. Optimizing Non-Matmul Operations

This is primarily referring to **softmax** operations.

### 2. Causal Masking

For any row number greater than the column number we do not need to calculate attention in decoder-based architectures. The FlashAttention-2 paper makes use of this optimization.

### 3. Increased Parallelization

Instead of parallelizing just the batches and over the number of heads, the FlashAttention-2 paper also **parallelizes over the sequence length**.

### 4. Worker Partitioning Between Warps

In the first scenario when the keys and values are partitioned, the final outcome is the result of communication between warps through shared memory. However, in the second part when the query is partitioned, the dot product with the keys can be calculated in a straightforward manner and then be used to scale the values.

## Results

FlashAttention and FlashAttention-2 lead to incredible memory and speed gains of **up to 10x and 2x** over standard PyTorch or Triton implementations.

## Strengths

FlashAttention and FlashAttention-2 lead to incredible memory and speed gains of up to 10x and 2x over standard PyTorch or Triton implementations.

## Weaknesses

The weakness is that this can work only with standard implementations of attention, however the authors are working on a compiler to make more custom implementations of attention faster with FlashAttention.

## Rating

**5/5** - Groundbreaking optimization techniques with significant practical impact on training and inference efficiency.
