---
permalink: /
title: "About me"
excerpt: "About me"
author_profile: true
redirect_from:
  - /about/
  - /about.html
---

## Featured Posts

### [Temperature in LLMs: From Softmax to Autoregressive Decoding](/posts/2026/01/temperature-in-llms/)
Understanding temperature's role in language model inference is crucial for controlling generation randomness. This deep dive explores how temperature scaling affects softmax probability distributions and impacts autoregressive decoding strategies from greedy to nucleus sampling.

---

I'm a Sr Research Engineer at Together AI, working on making inference faster at scale. Mostly that means speculative decoding—training models, cooking up data recipes, and building out training framework features as needed. Lately I've been digging into long-context training and model distillation.

Before this, I've worked at research labs (OLAB, NYU Langone & CVIT Lab, IIIT-Hyderabad) and industry data science gigs, working on things like information extraction, machine translation, and question-answering.

These days I'm most excited about RL for language models, systems work around attention, and multimodality. Also I keep an eye out for ML infrastructure.

<!-- Thoughts and Qs from my projects -->
<!-- ===== -->
{% for post in site.portfolio %}
  {% include archive-single.html type="grid" %}
{% endfor %}
