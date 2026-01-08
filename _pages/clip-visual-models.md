---
permalink: /talks/clip-visual-models/
title: "CLIP: Learning Transferable Visual Models from Natural Language Supervision"
excerpt: "Learning visual concepts from natural language supervision for zero-shot transfer"
author_profile: true
---

**Presented by:** Aman Singhal

**Venue:** Large Language and Vision Models Symposium, NYU Center for Data Science

**Year:** 2024

## Abstract

CLIP, a model that learns visual concepts directly from natural language supervision rather than fixed object categories. It's trained on 400 million image-text pairs from the internet using a simple task: predicting which caption matches which image. This approach enables zero-shot transfer to downstream tasks using natural language descriptions, matching ResNet-50's ImageNet accuracy without using any of its 1.28 million training examples.

## Presentation Slides

<div style="position: relative; width: 100%; padding-bottom: 56.25%; margin: 2rem 0;">
  <iframe src="https://docs.google.com/presentation/d/19rXMnZoJPE_acgC_AkjD6ix6bSVSlk6B8I9cKwYJXxk/embed?start=false&loop=false&delayms=3000"
          frameborder="0"
          width="100%"
          height="100%"
          allowfullscreen="true"
          mozallowfullscreen="true"
          webkitallowfullscreen="true"
          style="position: absolute; top: 0; left: 0;">
  </iframe>
</div>

[View slides in new window](https://docs.google.com/presentation/d/19rXMnZoJPE_acgC_AkjD6ix6bSVSlk6B8I9cKwYJXxk/edit?slide=id.p#slide=id.p)

## Key Topics

- Vision-language pretraining architecture
- Contrastive learning from image-text pairs
- Training on 400 million web-scraped pairs
- Zero-shot transfer to downstream tasks
- Natural language as flexible supervision signal
- Comparison with supervised learning approaches
- ImageNet performance without training examples
- Applications in multimodal AI and computer vision
- Impact on subsequent vision-language models
