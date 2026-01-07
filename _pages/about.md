---
permalink: /
title: ""
excerpt: "About me"
author_profile: true
redirect_from:
  - /about/
  - /about.html
---

I'm a Sr Research Engineer at Together AI, working on making inference faster at scale. Mostly that means speculative decoding—training models, cooking up data recipes, and building out training framework features as needed. Lately I've been digging into long-context training and model distillation.

Before this, I've worked at research labs (OLAB, NYU Langone & CVIT Lab, IIIT-Hyderabad) and industry data science gigs, working on things like information extraction, machine translation, and question-answering.

These days I'm most excited about RL for language models, systems work around attention, and multimodality. Also I keep an eye out for ML infrastructure.

---

## Featured Projects

<div class="projects-grid">

  <div class="project-card">
    <div class="project-card__image">
      <!-- Add your image here later: <img src="/images/retrieval-optimization.jpg" alt="Retrieval Optimization"> -->
    </div>
    <div class="project-card__content">
      <h3 class="project-card__title">Retrieval Optimization</h3>
      <div class="project-card__tags">
        <span class="project-card__tag">RAG</span>
        <span class="project-card__tag">Information Retrieval</span>
        <span class="project-card__tag">NLP</span>
      </div>
      <p class="project-card__description">
        Explored retrieval strategies for RAG pipelines including chunking optimization, sparse vs. dense embeddings (TF-IDF, DRAGON+, Sentence Transformers), and two-level retrieve-and-rerank approaches. Achieved <span class="project-card__highlight">78.6% Recall@10</span> through rechunking and similarity thresholding, with improved MRR via hybrid retrieval.
      </p>
      <div class="project-card__links">
        <a href="https://github.com/AmanSinghal927/RAG-prod" class="project-card__link">
          <i class="fab fa-github"></i>
          <span>Code</span>
        </a>
        <a href="https://github.com/AmanSinghal927/RAG-prod/blob/master/reports/AmanSinghal_report.pdf" class="project-card__link">
          <i class="fas fa-file-alt"></i>
          <span>Report</span>
        </a>
      </div>
    </div>
  </div>

  <div class="project-card">
    <div class="project-card__image">
      <!-- Add your image here later: <img src="/images/dpo-rlif.jpg" alt="DPO-RLIF"> -->
    </div>
    <div class="project-card__content">
      <h3 class="project-card__title">DPO-RLIF: Reinforcement Learning from Structurally-Derived Implicit Feedback</h3>
      <div class="project-card__tags">
        <span class="project-card__tag">PyTorch</span>
        <span class="project-card__tag">DPO</span>
        <span class="project-card__tag">Medical NLP</span>
        <span class="project-card__tag">RLHF</span>
      </div>
      <p class="project-card__description">
        LLMs in healthcare produce untrustworthy outputs due to biases from human annotators in RLHF and the difficulty of obtaining reliable alignment data at scale. We leverage the structural bias in multiple-choice questions to generate alignment data at medical licensing exam complexity, achieving <span class="project-card__highlight">+5.5% over SFT</span> on MMLU medical subsets and other clinical evaluation benchmarks.
      </p>
      <div class="project-card__links">
        <a href="https://github.com/AmanSinghal927/DPO-RL/tree/master" class="project-card__link">
          <i class="fab fa-github"></i>
          <span>Code</span>
        </a>
        <a href="https://kalpanmukherjee.github.io/dpo-rlif/" class="project-card__link">
          <i class="fas fa-file-alt"></i>
          <span>Paper</span>
        </a>
      </div>
    </div>
  </div>

  <div class="project-card">
    <div class="project-card__image">
      <!-- Add your image here later: <img src="/images/moe-gemm.jpg" alt="MoE GEMM"> -->
    </div>
    <div class="project-card__content">
      <h3 class="project-card__title">Exploring cuBLAS-Based Variable-Sized Batched GEMM for MoE Inference</h3>
      <div class="project-card__tags">
        <span class="project-card__tag">CUDA</span>
        <span class="project-card__tag">cuBLAS</span>
        <span class="project-card__tag">MoE</span>
        <span class="project-card__tag">Systems</span>
      </div>
      <p class="project-card__description">
        Mixture-of-Experts models suffer from inefficient batched computation due to dynamic routing and load imbalance. We developed variable-sized batched GEMM kernels using cuBLAS as an alternative to MegaBlocks' Triton-based sparse kernels. Our approach <span class="project-card__highlight">outperforms sparse MegaBlocks and torch.bmm</span> (which failed 30% of test cases due to OOM) while avoiding Triton's memory instability issues.
      </p>
      <div class="project-card__links">
        <a href="https://github.com/Aiden-Frost/megablocks" class="project-card__link">
          <i class="fab fa-github"></i>
          <span>MegaBlocks</span>
        </a>
        <a href="https://github.com/Aiden-Frost/grouped_gemm" class="project-card__link">
          <i class="fab fa-github"></i>
          <span>Grouped GEMM</span>
        </a>
      </div>
    </div>
  </div>

</div>
