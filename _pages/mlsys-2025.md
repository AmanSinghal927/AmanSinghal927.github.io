---
permalink: /conferences/mlsys-2025/
title: "MLSys 2025 - Conference Takeaways"
excerpt: "Key insights and learnings from MLSys 2025"
author_profile: true
---

[MLSys 2025 Conference Website](https://mlsys.org/Conferences/2025)

## A. Talks

### 1. Soumith Chintala (PyTorch)

[Watch Talk](https://mlsys.org/virtual/2025/invited-talk/2886)

**Key Insights:**
- North star has been how to break `for` loops into parallelism
- **Types of innovation in AI:**
  - Objective function
  - Better priors
  - Better labels (better data does not imply better labels)

**Torch Optimizations:**
- **Faster:** e.g. flex attention, torch.compile
- **Precision flexibility:** e.g. torchao (quantization)
- **Better reliability:** e.g. torchft, distributed checkpointing

**Extreme PyTorch (Llama 3 paper):**
- 24k GPUs
- Maximize effective training time
- Meta has large excel sheets of how to shard model

**Resources:**
- Best PyTorch features: [torchtitan](https://github.com/pytorch/torchtitan)
- Community: [GPU Mode Discord](https://discord.com/invite/gpumode)

**Future Directions:**
- ⭐️ **Writing kernels with LLMs**
  - [KernelBench](https://github.com/ScalingIntelligence/KernelBench)
  - [KernelLLM](https://huggingface.co/facebook/KernelLLM)

### 2. LLMArena

[LLMArena Website](https://llmarena.ai/)

**Key Insights:**
- Benchmarks should evolve with models
  - Static ones are unreliable due to contamination
  - Data freshness as a metric
- [Categorizing prompts into categories](https://blog.lmarena.ai/blog/2025/arena-explorer/)
- [Prompt specific leaderboard (P2L)](https://github.com/lmarena/p2l)

### 3. Scaling Laws - Beidi Chen

[Beidi Chen's Google Scholar](https://scholar.google.com/citations?user=jCNJhFcAAAAJ&hl=en)

**Key Insights:**
- Stage 3 was scaling context length; Stage 4 is test-time compute
  - How fast are we solving the whole problem?

**Notable Ideas:**
- **Multiverse 1K:** using map-reduce for decoding
- **Oversampling effective examples (DAPO):** [Paper](https://arxiv.org/html/2503.14476v1)

### 4. Tim Dettmers

**Key Insights:**
- **Notion of a good paper:** Does this paper share its assumptions?
- **Being Impactful:** Pick hard problems
  - Obsess over problems and not solutions or a particular kind of solution
- **Research Trends:** Cyclical nature (e.g., quantization had hit a wall a few years ago, revival with LLMs, and now is again about to hit a wall)

---

## B. Useful Papers

### Training Optimization

**[PIPEFILL: Using GPUs During Bubbles in Pipeline-Parallel LLM Training](https://arxiv.org/pdf/2410.07192)**
- A fill job is any independent workload temporarily executed during the idle bubbles in pipeline-parallel training to utilize wasted GPU time
- PIPEFILL anticipates bubbles called Pipeline Bubble Instruction, combined with profiling during the initial training iterations

**Key Parallelism Techniques:**

| Technique | Purpose |
|-----------|---------|
| **ZeRO** (1/2/3) | Memory optimization |
| **FSDP** | Fully Sharded Data Parallel |
| **Pipeline Parallelism** | Model is too big to fit even on a single node so it is partitioned across devices |

**[RADIUS: Range-Based Gradient Sparsity for Large Foundation Model Pre-training](https://openreview.net/pdf?id=UCQPWBOWb6)**
- Training large foundation models is extremely compute- and communication-heavy, especially when using data parallelism
- Naive solutions like top-k gradient sparsity don't scale and break convergence
- After an initial phase, reuse the same top-k indices for T steps
- Accumulate dropped gradients into a residual buffer

**[APOLLO: SGD-Like Memory, AdamW-Level Performance](https://arxiv.org/abs/2412.05270)**
- APOLLO (Approximated Gradient Scaling for Memory Efficient LLM Optimization)
- Uses a low-rank auxiliary optimizer state, 3× training throughput vs. AdamW
- Related: [GaLore](https://arxiv.org/abs/2403.03507)

**[REAL: Efficient RLHF Training of Large Language Models with Parameter Reallocation](https://arxiv.org/abs/2406.14088)**
- A task = a function call like generation, inference, or training, executed by one of the models (actor, critic, reward)
- Parameter Reallocation — dynamically redistribute model parameters across the cluster per task
- Up to 3.58x speedup vs baselines like DeepSpeed-Chat, OpenRLHF, and NeMo-Aligner

**[Mini-Sequence Transformer: Optimizing Intermediate Memory for Long Sequences Training](https://arxiv.org/pdf/2407.15892)**
- Partitions input sequences and iteratively processes mini-sequences
- Reduces intermediate memory usage with activation recomputation

### Inference Optimization

**[Enabling Unstructured Sparse Acceleration on Structured Hardware](https://openreview.net/forum?id=Py0XA6QQAh)**
- Express model weights/activations (online) as sum of sparse matrices, similar to Taylor series expansion
- Shows significant latency improvements
- Don't clearly specify accuracy/perplexity metrics, but idea is cool

**[MILO: Efficient Quantized MoE Inference with Mixture of Low-Rank Compensators](https://arxiv.org/abs/2504.02658)**
- Quantization helps reduce memory for MoE models but under 4-bit quantization causes non-trivial accuracy drops
- Milo: Quantize-then-Compensate with Low-Rank Matrices

**[Long Context - Offloading KV Cache](https://arxiv.org/abs/2502.12574)**
- Offloads the KV cache to CPU RAM

### Cluster Management

**[Rubick: Exploiting Job Reconfigurability for Deep Learning Cluster Scheduling](https://arxiv.org/pdf/2408.08586)**
- [GitHub](https://github.com/orgs/AlibabaPAI/repositories)
- Which job to allocate resources to based on marginal benefit of overall cluster saturation
- Treats jobs as white boxes

### Applied ML

**[KNOW WHERE YOU'RE UNCERTAIN WHEN PLANNING WITH MULTIMODAL FOUNDATION MODELS](https://arxiv.org/abs/2411.01639)**
- Use state-transition systems and temporal logic specifications like:
  - `(pedestrian → wait)` — "Always wait if there's a pedestrian"
  - `(red light → move forward)` — "Never move forward at a red light"
- Build reward models that score a trajectory based on how many constraints it violates

**[AIOPSLAB: A Holistic Framework to Evaluate AI Agents for Enabling Autonomous Clouds](https://arxiv.org/abs/2501.06706)**
- Agents are plethora for Dev but not for Ops because tasks are missing but 60% deployment failure comes from Ops problems
- Provide SRE dataset and interfaces for agents to interact with the cloud
- [Discussion](https://chatgpt.com/share/6823e04c-2e00-800f-b834-32f7deb184de)

**[AI METROPOLIS: Scaling Large Language Model-Based Multi-Agent Simulation with Out-of-Order Execution](https://arxiv.org/abs/2501.xxxxx)**
- Multi-agent simulations: all agents must finish a step before moving to the next
- Introduces out-of-order execution
- Maximum velocity: refers to the maximum distance an agent can influence with 1 simulation step

### Tools

**[LUMOS: Efficient Performance Modeling and Estimation for Large-Scale LLM Training](https://arxiv.org/abs/2504.09307)**
- Profiling tool for LLMs (code not found)
- Related profiling tools: [dpro](https://github.com/joapolarbear/dpro)

---

## C. Resources

**Books & Guides:**
- [JAX Scaling Book](https://jax-ml.github.io/scaling-book/)

**Tools:**
- [dpro - Profiling tools](https://github.com/joapolarbear/dpro)
- [torchtitan - Best PyTorch features](https://github.com/pytorch/torchtitan)
- [P2L - Prompt specific leaderboard](https://github.com/lmarena/p2l)

**Communities:**
- [GPU Mode Discord](https://discord.com/invite/gpumode)
