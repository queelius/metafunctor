---
title: "Autoregressive Models: Inductive Biases and Projections"
author: admin
summary: "This paper explores inductive biases and projection functions to enhance out-of-distribution generalization in autoregressive models. With the theory in place, we aim to implement these ideas in Python and develop tests and evaluations to validate our approach."
date: "2024-05-20"
slug: ngram-proj
external_url: "https://github.com/queelius/ngram-projections"
image:
  caption: Projections
  focal_point: Smart
  preview_only: yes
links:
- icon: github
  icon_pack: fab
  name: Code
  url: "https://github.com/queelius/ngram-projections"
tags:
- inductive bias
- out-of-distribution generalization
- maximum-likelihood-estimation
- likelihood-models
- data-generating-process
- bootstrap method
- large language models
- n-gram models
- infini-gram model
- statistics
categories:
- statistics
- data-science
- inference
- natural language processing
---

This paper explores the use of inductive biases and projection functions in autoregressive (AR) models to enhance out-of-distribution (OOD) generalization. We revisit the concept of infini-grams, which leverage suffix arrays to manage arbitrary input (context) lengths efficiently. This approach is compared to traditional n-gram models, highlighting its advantages in sample efficiency and computational scalability. We delve into various inductive biases, such as the recency bias, shortest edit distance, and semantic similarity, illustrating their impact on AR model performance. By framing OOD generalization as a projection problem, we propose strategies to optimize these projections through meta-learning and nested optimization. Furthermore, we discuss the integration of classical information retrieval techniques and pre-trained language model embeddings to enhance the semantic relevance of projections. Our findings suggest that combining symbolic AI methods with deep learning representations can yield more interpretable and sample-efficient AR models, with broad applications in natural language processing, code generation, and scientific discovery.

See the GitHub repo for both the paper and the in-development code.
