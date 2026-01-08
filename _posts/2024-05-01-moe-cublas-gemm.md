---
title: 'Improving MoE Inference with Variable-Sized Batched GEMM Kernels'
date: 2024-05-01
permalink: /posts/2024/05/moe-cublas-gemm/
excerpt: 'Mixture-of-Experts (MoE) models enhance efficiency and scalability of large-scale transformers, enabling sparse computation - dynamically routing data to a subset of experts. However, sparsity complicates efficient training & inference in batched settings. We propose a method to address the challenge. Our project aims to optimize MoE training by developing a variable-sized batched General Matrix-Matrix Multiplication (GEMM) kernel - using NVIDIA cuBLAS. Our approach offers an alternative kernel optimization for MoE compared to the Block Sparse Kernel proposed by MegaBlocks. We solve dynamic and imbalanced computation inherent in MoE architectures and improve training efficiency. By leveraging cuBLAS for variable-sized batched GEMM, we enhance the performance of sparse training in MoEs and provide a robust solution to the limitations of current frameworks.'
tags:
  - Mixture-of-Experts
  - CUDA
  - cuBLAS
  - Systems
  - Sparse Computation
---

**Team:** Aman Singhal, Rahul Raman, Srivats Poddar, Pranav Grandhi

## Abstract

Mixture-of-Experts (MoE) models enhance efficiency and scalability of large-scale transformers, enabling sparse computation - dynamically routing data to a subset of experts. However, sparsity complicates efficient training & inference in batched settings. We propose a method to address the challenge. Our project aims to optimize MoE training by developing a variable-sized batched General Matrix-Matrix Multiplication (GEMM) kernel - using NVIDIA cuBLAS. Our approach offers an alternative kernel optimization for MoE compared to the Block Sparse Kernel proposed by MegaBlocks. We solve dynamic and imbalanced computation inherent in MoE architectures and improve training efficiency. By leveraging cuBLAS for variable-sized batched GEMM, we enhance the performance of sparse training in MoEs and provide a robust solution to the limitations of current frameworks.

## Introduction

Mixture-of-Experts (MoE) models have emerged as a powerful approach to enhance the scalability and efficiency of large-scale transformers by using sparse computation. In an MoE architecture, each layer is composed of multiple experts, and data is dynamically routed to a subset of these experts based on a gating mechanism. This sparsity enables significant reductions in computational cost and training time, allowing MoEs to achieve state-of-the-art performance in both natural language processing and computer vision tasks.

However, the sparsity inherent in MoE models poses challenges for efficient training and inference, particularly in batched settings. Current frameworks, such as those used in the state-of-the-art MegaBlocks system, face limitations in handling the dynamic routing and load imbalance that characterize MoEs. These frameworks often resort to padding or dropping tokens to fit the computation into the constraints of existing hardware and software, leading to inefficiencies as well as potential loss in model quality.

Our project addresses these challenges by proposing an alternate solution to improve the efficiency of MoE training. The first solution involves the development of a variable-sized batched General Matrix-Matrix Multiplication (GEMM) kernel using NVIDIA's cuBLAS library. This approach offers an alternative kernel optimization for MoE, outperforming the Block Sparse Kernel proposed by MegaBlocks. By leveraging cuBLAS, we create a more efficient kernel that better handles the dynamic and imbalanced computation in MoEs, thereby enhancing training performance.

In the following sections we discuss the previously used methods for the state of the art MoE implementations.

### Batched Matrix Multiplication

In this method, state-of-the-art MoE implementations use batched matrix multiplication to compute all experts within a layer in parallel. The approach imposes constraints such that all experts must be assigned the same number of tokens and have the same shape, leading to inefficiencies when there is load imbalance amongst experts. While this method simplifies the implementation, it does not fully exploit the potential sparsity in MoEs, resulting in wasted computation and memory.

### Block Diagonal Matrix Multiplication

An alternative to batched matrix multiplication, this method reformulates expert computation as block diagonal matrix multiplication. Here, the experts are organized along the diagonal of a block-sparse matrix with identically sized blocks. This method helps in managing the imbalanced token assignments better than the previous approach. However, it still requires that all blocks are of equal size, which can limit its flexibility and efficiency.

### Block Sparse Matrix Multiplication

This method takes a step further by constructing a block diagonal matrix with variable-sized blocks made up of many smaller blocks. It allows for greater flexibility in handling load imbalances and dynamic routing in MoEs. The computation is expressed using block-sparse matrix multiplication, enabling efficient utilization of hardware resources and improving the overall training efficiency.

### Proposed Method: Variable-Sized Batched GEMM

Building on these methods, we propose a novel approach to further improve the efficiency of MoE training. Our solution involves developing a variable-sized batched General Matrix-Matrix Multiplication (GEMM) kernel using NVIDIA's cuBLAS library. This approach offers an alternative kernel optimization for MoE, outperforming the Block Sparse Kernel proposed by MegaBlocks. By leveraging cuBLAS, we aim to create a more efficient kernel that better handles the dynamic and imbalanced computation in MoEs, thereby enhancing training performance.

## Related Work

Improving the performance of a mixture-of-experts models is an active area of research. There have been many methods that were proposed to improve the performance of these MoE models. One area of active research is in the area of building high performance MoEs. These methods often focus on optimizing the distributed communication primitives for MoEs. One such notable method is Tutel.

### Tutel

Tutel is a high-performance framework developed by Microsoft to optimize the training of Mixture-of-Experts (MoE) models, especially across thousands of GPUs. MoE models dynamically route each token to a subset of experts, leading to variable workloads. Tutel introduces several innovations to manage this complexity and enhance performance. Tutel adjusts parallelism and pipelining strategies at runtime to optimize resource use. This dynamic adjustment results in up to 1.74x speedup from parallelism switching and 2.00x from pipelining. It also employs a two-dimensional hierarchical algorithm to optimize data transfer between GPUs, achieving up to 20.7x speedup in communication efficiency over 2,048 GPUs. It has demonstrated substantial improvements in training efficiency, achieving up to 4.96x and 5.75x speedup of a single MoE layer on 16 GPUs and 2,048 GPUs, respectively, over Fairseq. These results highlight Tutel's efficiency across different hardware configurations, making it a robust choice for large-scale MoE training.

### FasterMoE

Another system, FasterMoE, proposes a different approach to distributed training of MoEs. It focuses on efficient communication strategies and modifications to the MoE routing algorithm to avoid network congestion. This system introduces shadow experts and smart scheduling to balance the load and enhance throughput, although the benefits are conditional and depend on the token distribution.

### Blocked Compressed Sparse Row (BCSR)

Lastly, another area of improvement is efficiently utilizing sparse kernels for faster training/inferencing. Sparse matrix formats that allow for efficient transposed access are crucial for optimizing the performance of MoEs on modern hardware. These formats have been well-studied in the context of sparse computations.

BCSR divides the matrix into fixed-size blocks, storing only the non-zero blocks, which enhances memory efficiency and cache utilization by optimizing data access patterns and reducing overhead. This format simplifies indexing and data retrieval, crucial for high-performance applications. Related formats, like Blocked Coordinate (BCOO) and Blocked Compressed Sparse Column (BCSC), also aim to optimize sparse matrix operations by organizing non-zero elements into blocks, balancing storage efficiency and access speed. These formats significantly improve the performance of sparse computations by minimizing memory accesses and overhead, making them well-suited for modern deep learning models, including MoEs.

## Design and Implementation

### GroupedBatched MegaBlock Framework

The illustration showcases the key implementation differences between the megablock's sparse GEMM and our variable-sized GEMM. MegaBlocks code-flow is as follows:

1. **Router:** A feedforward neural network determines token assignment to respective experts
2. **Padded_gather:** Tokens from multiple sequences are grouped based on router assignment
3. **Topology:** Sparse matrix topology indicating the required cells of the output matrix
4. **MLP:** Sparse matrix multiplication is performed using Triton kernels, with the SDD and DSD kernels utilized in the feedforward layers
5. **Padded_scatter:** Post-matrix multiplication, tokens are regrouped into their original sequences

A notable challenge with this approach is the computation overhead associated with topology calculation. Our proposed approach retains the fundamental structure of the mega block implementation but substitutes Triton kernels with cuBLAS kernels. This substitution eliminates the necessity of computing topology. The paper has also implemented its own sophisticated and optimized version of grouped multiplication compared to our approach.

### Our GroupedBatched Kernels in cuBLAS

We implemented two kernels in cuBLAS one using `cublasGemmEx` and the other using `cublasSgemmGroupedBatched`. Given `bsizes`, an array containing batch sizes, we can assume `z = sum(bsizes)` and `bs = bsizes.size()`. The fundamental idea both of these kernels utilize is the fact that, given a matrix A of shape `(z * m) * k` to be batch multiplied with a tensor B of shape `bs * k * n`, we can split A into `A_array = [bsizes[0] * m * k, bsizes[0] * m * k, ..., bsizes[bs-1] * m * k]` and B into `B_array = [k * n, k * n, ..., k * n]` and then compute `C_array[i]` as `A_array[i] * B_array[i]`, which can then be finally stacked to make C of shape `(z * m) * n`.

Our kernel based on `cublasGemmEx` utilises a loop to split A and B into respective arrays, and within the loop calls `cublasGemmEx` to compute the output. However recently, in the latest version, cuBLAS came up with an optimized kernel `cublasSgemmGroupedBatched` for the same which accepts `A_array` and `B_array`. Hence, in our second kernel we use a loop to split A and B into respective arrays and directly call this kernel to compute the output.

## Evaluation

Our experiment entails a comprehensive evaluation of our pipeline against megablock's sparse and grouped implementations, alongside a comparison with MoE using `torch.bmm`. Installation instructions for megablocks on NYU HPC are available on our GitHub repository. All experiments were conducted on NYU Greene using V100 GPU with 16 GB VRAM and CUDA Version 12.4.

We validated our kernel implementation by cross-referencing our output matrix with `torch.bmm`'s output matrix. Extensive testing was conducted across various intensities of batch matrix multiplication. Test cases were structured as `(num_of_sequences, sequence_length, hidden_size, num_experts, top_k_MoE)`, with the most intense test case reaching a size of `(64, 1024, 4224, 8, 3)` before encountering out-of-memory issues. MoE weights and input matrices were represented in half and float32 precision.

### Results

In our experimentation, we observed that our implementation **outperformed the sparse implementation of megablocks** and MoE using the `torch.bmm` method. However, it was comparatively slower than the grouped implementation of megablocks. Notably, the MoE implementation using `torch.bmm` **encountered memory issues, passing only 70% of the test cases** compared to the other baselines. We posit that our implementation's slower performance relative to the grouped implementation may be attributed to their memory-optimized strategy, which involves storing input and activation output of respective layers in torch context for overall speedup in a training iteration.

### Notable Findings

During our experiments, we encountered two noteworthy anomalies:

**1. Batched GEMM Efficiency:** Surprisingly, batched GEMM computations for unevenly distributed batch sizes across experts proved faster than those with evenly distributed batch sizes. For example, in a test case with dimensions `(64, 1024, 512, 8, 8)`, where the total number of tokens is calculated as `num_of_sequences * sequence_length * top_k_MoE = 64 * 1024 * 8 = 524288` tokens. We observed that distributing tokens unevenly `[130759, 78925, 35368, 90425, 1433, 120455, 64589, 2134]` was faster than uniform distribution `[65536, 65536, 65536, 65536, 65536, 65536, 65536, 65536]`. We speculate that larger cuBLAS kernels may perform more efficiently than medium-sized kernels, and possibly due to the iterative nature of our batched GEMM implementation.

**2. Issues with Triton Kernels:** Our experimentation with megablocks revealed reliability issues with Triton kernels used for batch matrix multiplication and gather-scatter implementation in MoE. For instance, in a test case with dimensions `(32, 512, 512, 8, 4)`, we encountered a RuntimeError indicating a Triton Error `[CUDA]: an illegal memory access`. Similar issues have been mentioned on GitHub, indicating potential instability in Triton kernel functionality.

Throughout our experiments, we exclusively tested our `cublasGemmEx` kernel due to unresolved issues with our `cublasSgemmGroupedBatched` kernel (The kernel functions properly for fixed batch sizes; however, discrepancies arise in the final matrix values when variable batch sizes are used, as with comparison to `torch.bmm` final matrix.). Our testing focused solely on forward propagation, as we encountered no discrepancies in backward propagation final loss when compared to the standard `torch.bmm` MoE model. We were able to fix our discrepancies in backward propagation by using `grouped_gemm` backend framework and using our kernel (updated the fork of the repo as a submodule in our repo).

## Key Contributions

- Developed variable-sized batched GEMM kernels using cuBLAS as an alternative to Triton-based sparse kernels
- Outperformed sparse MegaBlocks implementation in terms of speed and memory efficiency
- Avoided memory instability issues present in Triton kernels
- Successfully handled 100% of test cases compared to `torch.bmm`'s 70% success rate
- Discovered that uneven batch size distribution can lead to better performance than uniform distribution

## Project Code

Our code is publicly available at:
- [MegaBlocks Fork](https://github.com/Aiden-Frost/megablocks)
- [Grouped GEMM Fork](https://github.com/Aiden-Frost/grouped_gemm)

To run our code and reproduce our results, follow this guide for setup: [NYU HPC Setup Guide](https://github.com/Aiden-Frost/megablocks/tree/main/nyu_hpc_setup)

For exact commit changes:
- [MegaBlocks Comparison](https://github.com/databricks/megablocks/compare/main...Aiden-Frost:megablocks:main)
- [Grouped GEMM Comparison](https://github.com/tgale96/grouped_gemm/compare/main...Aiden-Frost:grouped_gemm:main)

## Citations

1. Hwang, C., Cui, W., Xiong, Y., Yang, Z., Liu, Z., Hu, H., ... & Xiong, Y. (2023). Tutel: Adaptive mixture-of-experts at scale. *Proceedings of Machine Learning and Systems*, 5.

2. Shazeer, N., Mirhoseini, A., Maziarz, K., Davis, A., Le, Q., Hinton, G., & Dean, J. (2017). Outrageously large neural networks: The sparsely-gated mixture-of-experts layer. *Proceedings of the International Conference on Learning Representations*.

3. Lepikhin, D., Lee, H., Xu, Y., Chen, Z., Firat, O., Huang, Y., ... & Chen, D. (2021). GShard: Scaling giant models with conditional computation and automatic sharding. *Proceedings of the International Conference on Machine Learning*.

4. Fedus, W., Zoph, B., & Shazeer, N. (2021). Switch Transformers: Scaling to trillion parameter models with simple and efficient sparsity. *arXiv preprint arXiv:2101.03961*.

5. NVIDIA. (2023). cuBLAS Library User Guide. NVIDIA Corporation.

6. NVIDIA. NVIDIA cuSPARSE Library. https://developer.nvidia.com/cusparse, 2022b.

7. NVIDIA. NVIDIA CUTLASS Library. https://github.com/NVIDIA/cutlass, 2022c

8. He, J., Zhai, J., Antunes, T., Wang, H., Luo, F., Shi, S., & Li, Q. (2022, April). Fastermoe: modeling and optimizing training of large-scale dynamic pre-trained models. In *Proceedings of the 27th ACM SIGPLAN Symposium on Principles and Practice of Parallel Programming* (pp. 120-134).

9. scipy.sparse.bsr_matrix — SciPy v1.13.0 Manual

10. Gale, T., Narayanan, D., Young, C., & Zaharia, M. (2023). Megablocks: Efficient sparse training with mixture-of-experts. *Proceedings of Machine Learning and Systems*, 5.
