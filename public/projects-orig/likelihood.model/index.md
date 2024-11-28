---
title: "likelihood.model R package"
author: admin
summary: "An API for constructing and working with likelihood models; works well with `algebraic.mle` R package"
date: "2023-04-10"
slug: likelihood-model
external_url: "https://github.com/queelius/likelihood.model"
image:
  caption: In all likelihood!
  focal_point: Smart
  preview_only: yes
links:
- icon: github
  icon_pack: fab
  name: Code
  url: "https://github.com/queelius/likelihood.model"
- icon: paper-plane
  icon_pack: fas
  name: Docs
  url: "https://likelihood-model.metafunctor.com"
tags:
- maximum-likelihood-estimation
- likelihood-models
- likelihood-contributions-model
- R-package
- Fisher-information matrix
- data-generating-process
- R
- statistics
categories:
- statistics
- data-science
- inference
- survival-analysis
- reliability-analysis
---

The R packge `likelihood.model` provides an API for specifying likelihood models
for statistical inference.

The basic likelihood model is a concept that, in order for your object to satisfy,
must implement a number of functions (generic methods). The package provides two
different implementations of the concept:

1. `likelihood_contr_model` is a flexible framework for specifying likelihood models
based on the idea of independent likelihood contributions for different types of observations, e.g., right-censored versus exact observations,
or other kinds observations. This model is designed to accomodate more specialized likelihood models, such as series
systems with latent commponents which includes ambiguous data about the components, such as
masked failure causes.

2. `likelihood_name_model` provides a convenient wrapper for distribution functions that follow the
naming and argument conventions in the R ecosystem, e.g., if we have some distribution `norm` (normal),
then it has `dnorm`, `pnorm`, `rnorm`, and `qnorm`, respectively for the density function, probability
function, sampler, and quantile function for the normal distribution. They also have standard paramenter
arguments, like `pnorm` has a `lower.tail` Boolean parameter that computes either the CDF if TRUE and
otherwise the survival function. Note that this model may be used to provide contributions to `likelihood_contr_model`.

The package is designed to be used with the `algebraic.mle` package, which provides a framework for performing maximum likelihood estimation (MLE).

