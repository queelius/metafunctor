---
title: "femtograd R package"
author: Alex Towell
email: lex@metafunctor.com
summary: "An R package for backpropagation through a computational graph"
date: "2023-03-29"
layout: "project"
links:
- name: GitHub
  url: "https://github.com/queelius/femtograd"
- name: API Docs
  url: "https://queelius.github.io/femtograd"
tags:
- maximum-likelihood-estimation
- R
- automatic-differentiation
- backpropagation
- computational-graph
- statistics
- data-science
- inference
- likelihood-models
- machine-learning
- optimization
---

The R package `femtograd` provides a way of doing automatic differentation.
It's not particularly fast, as it was just an experiment, but I may end up using
it in my `likelihood.model` or `algebraic.mle` packages.

I will probably optimize it and provide a C++ implementation using Rcpp, but
I don't want to spend too much time on it since there are already nice packages
that do this.