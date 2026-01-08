---
permalink: /projects/retrieval-optimization-report/
title: "Retrieval Optimization for RAG Pipelines"
excerpt: "Exploring chunking, retrieval strategies, and evaluation metrics for RAG systems"
author_profile: true
---

[View Code on GitHub](https://github.com/AmanSinghal927/RAG-prod)

---

## Introduction

In this project, we aim to extract context for RAG from PDF documents. Key directions explored include:

- **Parsing/chunking:** e.g., correcting over-chunking
- **1-level & 2-level search:** retrieve / retrieve and re-rank
- **Evaluation metrics:** e.g., Recall@k, MRR
- **Zero/Single/Multiple retrieved contexts**

## Models

We consider FAISS FlatIndex due to our small corpus size and experiment with embedders:

1. **TF-IDF/BM25** (level-1) and **DRAGON+** (re-ranker)
2. **DRAGON+**
3. **TF-IDF/BM25**
4. **Sentence Transformer**

## Labelling

Questions are manually labelled with corresponding answer contexts and tagged as table or paragraph-based questions.

### Answer Dependency Distribution (Ground Truth)
- **Single Answer:** 81.8%
- **Multiple Answers:** 12.1%
- **No Answer:** 6.1%

### Answer Type Distribution (Ground Truth)
- **Paragraph-based:** Majority
- **Table-based:** Minority

## Metrics

As noted in "RAG: Lost in the Middle" [1]: Most likely chunks should be at the start of a context.

- **Recall@10:** We capture all positives, providing relevant context to an LLM
- **MRR (Mean Reciprocal Rank):** Position more relevant contexts earlier

## Results & Analysis

### 1. Correcting Over-Chunking

**Finding:** Rechunking reduces drop in recall due to incomplete chunks.

**Revised recall matching criteria:**
- Fuzzy similarity (ground truth, retrieved context) > 0.9
- len(retrieved context) > 0.95 × len(ground truth)
- Accounts for cases when ground truth ⊆ retrieved context

| Method | Recall@10 |
|--------|-----------|
| Original Chunking | ~30% |
| Rechunking | ~40% |

**Improvement:** +33% relative increase

### 2. Level-1 Retrieval: Comparing Dense and Sparse Embedders

**Finding:** Dense retrieval discourages n-gram matches; all given questions require n-gram match.

| Embedder | Recall@10 |
|----------|-----------|
| DRAGON+ | 7.0% |
| Sentence Transformer | 42.9% |
| TF-IDF | 64.3% |

**Key Insight:** Sparse retrieval (TF-IDF) significantly outperforms dense methods for this task due to the importance of n-gram matching.

### 3. No Answer Threshold: Reducing LLM Hallucination

**Finding:** Thresholding sets retrieved contexts to None when answer context may not be present.

| Method | Recall@10 |
|--------|-----------|
| No thresholding | 64.3% |
| Rechunking & thresholding | 78.6% |

**Threshold:** Median level-1 similarity between retrieved chunk and query, when ground truth is NULL set.

**Improvement:** +22% relative increase

### 4. TF-IDF/BM25 vs 2-Level (TF-IDF/BM25 & DRAGON+)

**Finding:** 2-level retrieval improves MRR while Recall@10 remains the same.

| Method | MRR | Recall@10 |
|--------|-----|-----------|
| 1-level (TF-IDF) | 0.44 | 73.9% |
| 2-level (TF-IDF + DRAGON+ rerank) | 0.65 | 73.9% |

**Key Insight:** 2-level retrieval comprises TF-IDF sparse retrieval & Dragon dense re-ranking, improving ranking quality without sacrificing recall.

**Improvement:** +48% relative MRR increase

### 5. Result on DR--185549702_INTRO

| Metric | Value |
|--------|-------|
| MRR | 0.5 |
| Recall@10 | 70.0% |

## Future Work & Error Analysis

### 1. Chunking
- **In-house document segmentation** or implementation of Parent Retriever
- Reference: [LangChain Parent Document Retriever](https://python.langchain.com/docs/modules/data_connection/retrievers/parent_document_retriever/)

### 2. Retrieval
- Explore **SPLADE** and other methods from [MTEB Leaderboard](https://huggingface.co/spaces/mteb/leaderboard)
- Further optimization of hybrid retrieval strategies

## Key Takeaways

1. **Rechunking significantly improves recall** by addressing incomplete chunk issues
2. **Sparse retrieval (TF-IDF) outperforms dense methods** for n-gram dependent queries
3. **Similarity thresholding reduces false positives** and potential LLM hallucination
4. **Two-level retrieval** (sparse + dense reranking) improves result quality (MRR) while maintaining coverage
5. **Final best performance:** 78.6% Recall@10 through rechunking and thresholding

## References

[1] Nelson F. Liu, Kevin Lin, John Hewitt, Ashwin Paranjape, Michele Bevilacqua, Fabio Petroni, Percy Liang. **Lost in the Middle: How Language Models Use Long Contexts.** Transactions of the Association for Computational Linguistics 2024; 12:157–173. doi: https://doi.org/10.1162/tacl_a_00638

[2] SC Lin, A Asai, M Li, B Oguz, J Lin, Y Mehdad, W Yih, X Chen. **How to Train Your DRAGON: Diverse Augmentation Towards Generalizable Dense Retrieval.** arXiv preprint arXiv:2302.07452
