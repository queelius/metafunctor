---
title: "Infini-gram: LLM-scale *-gram models"
author: admin
date: '2024-05-20'
slug: infini-gram
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: [ngram-proj]
---

Recently, I watched a presentation on [Infini-grams](https://huggingface.co/spaces/liujch1998/infini-gram), which utilize a suffix array to avoid precomputing $n$-grams and allow for arbitrary context lengths, up to a suffix that is found in the training data.

This sparked my interest as I had worked on a similar project for a LLM talk I gave for SLUUG at [https://www.stllinux.org](https://www.stllinux.org/) (see my GitHub repo [https://github.com/queelius/sluug-talk-llm](https://github.com/queelius/sluug-talk-llm) and the video fo the talk at [https://www.sluug.org/resources/presentations/media/2024/STLLINUX/2024-02-22_STLLINUX_2560x1440.mp4](https://www.sluug.org/resources/presentations/media/2024/STLLINUX/2024-02-22_STLLINUX_2560x1440.mp4)) where in part of the talk I demonstrated arbitrary-size $n$-grams using a recursive dictionary to store synthetic training data prefix counts to implement a crude expression tree evaluator.

Since my data was sparse synthetic data (expression trees and their evaluations), I was able to use a relatively inefficient approach to compute very large $n$-grams. The infini-gram approach is more efficient and generalizes to any kind of data, so they definitely had a more practical solution.

I started a project, [n-gram projections](ngram-proj), to work on concepts related to n-grams and how projections of the input onto the training data may be a way of thinking about OOD generalization and inductive biases. See ...

