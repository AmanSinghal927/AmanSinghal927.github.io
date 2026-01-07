---
permalink: /
title: ""
excerpt: "About me"
author_profile: true
redirect_from:
  - /about/
  - /about.html
---

## About Me

I'm a Sr Research Engineer at [Together AI](https://www.together.ai/), working on making inference faster at scale. Mostly that means speculative decoding—training models, cooking up data recipes, and building out training framework features as needed. Lately I've been digging into long-context training and model distillation.

Before this, I've worked at research labs ([OLAB](https://www.nyuolab.org/), NYU Langone & [CVIT Lab](https://cvit.iiit.ac.in/), IIIT-Hyderabad) and industry data science gigs, working on things like information extraction, machine translation, and question-answering.

These days I'm most excited about RL for language models, systems work around attention, and multimodality. Also I keep an eye out for ML infrastructure.

Check out my [CV](/cv/) for more detailed info.

## Featured Projects

<div class="projects-grid">

  <div class="project-card">
    <div class="project-card__tooltip">
      Explored retrieval strategies for RAG pipelines including chunking optimization, sparse vs. dense embeddings (TF-IDF, DRAGON+, Sentence Transformers), and two-level retrieve-and-rerank approaches. Achieved 78.6% Recall@10 through rechunking and similarity thresholding.
    </div>
    <div class="project-card__image">
    </div>
    <div class="project-card__content">
      <h3 class="project-card__title">Retrieval Optimization</h3>
      <div class="project-card__links">
        <a href="https://github.com/AmanSinghal927/RAG-prod" class="project-card__link">
          <i class="fab fa-github"></i>
        </a>
        <a href="https://github.com/AmanSinghal927/RAG-prod/blob/master/reports/AmanSinghal_report.pdf" class="project-card__link">
          <i class="fas fa-file-alt"></i>
        </a>
      </div>
    </div>
  </div>

  <div class="project-card">
    <div class="project-card__tooltip">
      LLMs in healthcare produce untrustworthy outputs due to biases from human annotators in RLHF. We leverage structural bias in multiple-choice questions to generate alignment data at medical licensing exam complexity, achieving +5.5% over SFT on MMLU medical subsets.
    </div>
    <div class="project-card__image">
    </div>
    <div class="project-card__content">
      <h3 class="project-card__title">DPO-RLIF: Reinforcement Learning from Structurally-Derived Implicit Feedback</h3>
      <div class="project-card__links">
        <a href="https://github.com/AmanSinghal927/DPO-RL/tree/master" class="project-card__link">
          <i class="fab fa-github"></i>
        </a>
        <a href="https://kalpanmukherjee.github.io/dpo-rlif/" class="project-card__link">
          <i class="fas fa-file-alt"></i>
        </a>
      </div>
    </div>
  </div>

  <div class="project-card">
    <div class="project-card__tooltip">
      Mixture-of-Experts models suffer from inefficient batched computation due to dynamic routing and load imbalance. We developed variable-sized batched GEMM kernels using cuBLAS as an alternative to MegaBlocks' Triton-based sparse kernels. Outperforms sparse MegaBlocks and torch.bmm.
    </div>
    <div class="project-card__image">
    </div>
    <div class="project-card__content">
      <h3 class="project-card__title">Exploring cuBLAS-Based Variable-Sized Batched GEMM for MoE Inference</h3>
      <div class="project-card__links">
        <a href="https://github.com/Aiden-Frost/megablocks" class="project-card__link">
          <i class="fab fa-github"></i>
        </a>
        <a href="/posts/2026/01/moe-cublas-gemm/" class="project-card__link">
          <i class="fas fa-file-alt"></i>
        </a>
      </div>
    </div>
  </div>

</div>

## Paper Digest

{% assign recent_posts = site.posts | limit: 3 %}
{% for post in recent_posts %}
[{{ post.title }}]({{ post.url }})
{{ post.date | date: "%B %d, %Y" }}

{% endfor %}

<span style="text-decoration: underline;">[View all →](/paper-digest/)</span>

<script>
// Mobile tap-to-reveal for project tooltips
document.addEventListener('DOMContentLoaded', function() {
  if (window.innerWidth <= 768) {
    const projectCards = document.querySelectorAll('.project-card');

    projectCards.forEach(function(card) {
      card.addEventListener('click', function(e) {
        // Don't trigger if clicking a link
        if (e.target.closest('.project-card__link')) {
          return;
        }

        e.stopPropagation();

        // Close other cards
        document.querySelectorAll('.project-card').forEach(function(otherCard) {
          if (otherCard !== card) {
            otherCard.classList.remove('show-description');
          }
        });

        // Toggle current card
        card.classList.toggle('show-description');
      });
    });

    // Close when clicking outside
    document.addEventListener('click', function(e) {
      if (!e.target.closest('.project-card')) {
        document.querySelectorAll('.project-card').forEach(function(card) {
          card.classList.remove('show-description');
        });
      }
    });
  }
});
</script>
