---
title: 'Flamingo: a Visual Language Model for Few-Shot Learning'
date: 2026-01-06
permalink: /posts/2026/01/flamingo-visual-language-model/
excerpt: "The paper's primary contribution is leveraging a pre-trained language model for the development of a multimodal model. This model, which they term a 'visual language model', brings forth two main points of innovation. Firstly, it's adept at handling interleaved images and texts of variable lengths. The importance of interleaving? It's paramount because that's the structure of most online data (data availability). What's the endgame for the authors? They're aiming for a model that can tackle a new task with just a few examples or instructions (few-shot capabilities). A noteworthy mention is that previous methods didn't quite hit the mark in low-data scenarios (previous-work). This approach is pioneering in its focus on few-shot learning."
tags:
  - Vision-Language Models
  - Few-Shot Learning
  - Multimodal
  - CLIP
---

## Summary

**Introduction and Significance:** The paper's primary contribution is leveraging a pre-trained language model for the development of a multimodal model. This model, which they term a "visual language model", brings forth two main points of innovation. Firstly, it's adept at handling interleaved images and texts of variable lengths. The importance of interleaving? It's paramount because that's the structure of most online data (data availability). What's the endgame for the authors? They're aiming for a model that can tackle a new task with just a few examples or instructions (few-shot capabilities). A noteworthy mention is that previous methods didn't quite hit the mark in low-data scenarios (previous-work). This approach is pioneering in its focus on few-shot learning.

**Model in a Nutshell:** They've crafted visually conditioned auto-regressive text generation models. These models are primed to ingest a sequence of text tokens, which are seamlessly interleaved with images and/or videos, and the end product? Textual output.

**High-Level Approach:** Kickstarting with the AIM, the objective is clear: generate tokens auto-regressively. The challenges? Encoding the image and pinpointing the apt insertion points for those image tokens, especially when few-shot learning is in the context. The first hurdle is tackled by the image encoder in tandem with the perceiver resampler. The image encoder's training is reminiscent of the CLIP model, where similar and dissimilar pairs of text and image embeddings play a pivotal role in a contrastive approach. Videos in the mix? They're sampled at regular intervals to produce a set of images. An intriguing detail is the dual cross-entropy losses: one from image to text and the other from text to image. The visual encoder's role is crystal clear: digest visual input and churn out embeddings, all set for a text generation task.

**The Perceiver Resampler:** This component is a captivating architectural setup, drawing inspiration from the "perceiver" paper. The perceiver tackles the computational complexity challenge that Transformers grapple with, especially with image modalities. The crux? While a sentence might stretch to a maximum of 1000 tokens, visual encoders like Vision Transformers have to juggle each pixel as an individual token. The math? A 224x224 image translates to 50,000+ Transformer tokens. The solution proposed by the perceiver authors? A proxy query matrix with a significantly reduced dimension compared to the original image's pixel count or embeddings. The image is consistently inserted as a signal to reinforce the learning objective.

**Back to Flamingo:** Post the visual encoder's magic, which might produce variable-length embeddings (thanks to videos), the perceiver resampler steps in. Its mission? Condense that into a fixed-size embedding or (per-shot), in other words, craft the query matrix for cross attention.

**Integration with Pre-Trained Model:** With the query matrix in hand, the next step is weaving in the cross-attention layers with the pre-trained language model's layers. A pivotal hyperparameter here is the frequency of this interleaving. The authors' experiments spanned various frequencies, with the best performance achieved by interleaving every four layers for smaller models and every seven layers for larger ones. A salient point? Each image has its unique embedding, courtesy of the visual encoder and perceiver resampler. But the real game-changer? How these layers are interleaved. It's not a mere append operation. The introduction of a tanh(α) activation, where α is a learnable parameter initialized to 0, ensures that at the outset, the model is purely a language model. As α evolves, the model learns to harness both the vision layers of the cross-attention and the frozen layers of the pre-trained language model. "We ablate the use of the 0-initialized tanh gating when merging the cross-attention output to the frozen LM output in row (iii). Without it, we see a drop of 4.2% in our overall score."

**Masking for Auto-Regressive Generation:** Ensuring the right masking is paramount for auto-regressive generation. The goal? Tokens corresponding to a specific image should be the sole participants in cross attention. For instance, in a few-shot scenario with an example like "this is a cat" followed by a cat image and then "this is a?" with a dog image, the cat image should only be attended to by the tokens in the first example. The second example's tokens, which require an answer, should focus on the dog image. The magic wand? Self attention, which facilitates knowledge transfer. This modification empowers the model to handle as many as 32 few-shot examples without a dip in performance. A revelation, given that the model was originally trained to manage around 5 images with their corresponding text inputs.

## Strengths

The paper is able to leverage a pre-trained LLM for multi-modal tasks. The model is capable of few-shot learning. Moreover, the model can work with videos using the perceiver sampler. SoTA with fine-tuning on several tasks.

## Weaknesses

- **Weakness of in-context learning:** it is highly dependent on the kind of prompt used
- **Highly dependent on the right data mix and the quality of the data as well**
- **Do not do well as compared to SoTA models with few-shot learning:** However, VizDial seems noisy and extremely artificial for such a task. Moreover, does not do really well on video-based tasks

## Benchmarks Evaluated

- **VQVA:** Visual question answering
- **COCO:** Object detection & segmentation
- **VATEX:** Video Captioning
- **VizWiz:** Images taken by blind people asking a description of that image
- **Microsoft Research Video to Text Question Answering**
- **YouCook2**
- **VisDial**

## Rating

**5/5** - This is a very well-thought out architecture, especially the integration of video and image was a really nice touch. Moreover, the Perceiver Sampler and experimentation with inter-leaving was necessary and thoroughly investigated. Finally, the paper discusses its short-comings thoroughly. However, an artifact of shortcoming was not achieving performances comparable to sota on all tasks with few-shot in-context learning.
