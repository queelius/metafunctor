---
title: "Fine-Tuning Tiny LLMs for ElasticSearch DSL"
author: admin
slug: llm-fine-tuning-es-dsl
summary: "I am creating a tiny LLM for ElasticSearch DSL as a proof of concept."
date: "2024-02-19"
external_url: https://github.com/queelius/elasticsearch-lm
image:
  caption: ElasticSearch DSL Tiny LLM
  focal_point: Smart
  preview_only: yes
links:
- icon: github
  icon_pack: fab
  name: Code
  url: "https://github.com/queelius/elasticsearch-lm"
tags:
- large language models
- fine-tuning
- information retrieval
- elastic search
- domain-specific language
- json
categories:
- statistics
- machine learning
- inference
---

I am fine-tuning a tiny LLM for ElasticSearch DSL as a proof of concept. The
GitHub repo for this project can be found [here](https://github.com/queelius/elasticsearch-lm).
It mostly consists of synthetic data. I need to reshape the data so that it's
in the expected format and then fine-tune the model, as the data has been
generated, initially from GPT-4 and then from a script I made to sample from
those outputs and use them as few-shot examples for Mistral to generate
a lot more synthetic data. I will then use this data to fine-tune the model
and see how well it performs on the ElasticSearch DSL.

Shoot me an email at [lex@metafunctor.com](mailto:lex@metafunctor.com) if you're
interested in collaborating on any projects.
