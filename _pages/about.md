---
permalink: /
title: "Aman Singhal"
excerpt: "About me"
author_profile: true
redirect_from:
  - /about/
  - /about.html
---

## About Me

I'm a Sr Research Engineer at Together AI, working on making inference faster at scale. Mostly that means speculative decoding—training models, cooking up data recipes, and building out training framework features as needed. Lately I've been digging into long-context training and model distillation.

Before this, I've worked at research labs (OLAB, NYU Langone & CVIT Lab, IIIT-Hyderabad) and industry data science gigs, working on things like information extraction, machine translation, and question-answering.

These days I'm most excited about RL for language models, systems work around attention, and multimodality. Also I keep an eye out for ML infrastructure.

---

## Featured Projects

<div class="projects-grid">

  <div class="project-card">
    <div class="project-card__image">
      <!-- Add your image here later: <img src="/images/ml-algorithms.jpg" alt="ML Algorithms"> -->
    </div>
    <div class="project-card__content">
      <h3 class="project-card__title">ML Algorithms from Scratch</h3>
      <div class="project-card__tags">
        <span class="project-card__tag">Python</span>
        <span class="project-card__tag">NumPy</span>
        <span class="project-card__tag">ML Theory</span>
      </div>
      <p class="project-card__description">
        Building core machine learning algorithms from first principles—understanding the math, implementing the code, and exploring the trade-offs. From gradient descent optimizers to neural network architectures, this project digs into what makes ML tick under the hood.
      </p>
      <div class="project-card__links">
        <a href="#" class="project-card__link">
          <i class="fab fa-github"></i>
          <span>Code</span>
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

</div>
