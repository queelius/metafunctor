---
title: "R Package: Dynamic failure rate (DFR) distributions"
author: admin
summary: "An API for constructing and working with dynamic failure rates."
date: "2023-01-11"
slug: dfr-dist
external_url: "https://github.com/queelius/dfr_dist"
image:
  caption: Autoregressive failure rate distributions!
  focal_point: Smart
  preview_only: yes
links:
- icon: github
  icon_pack: fab
  name: Code
  url: "https://github.com/queelius/dfr_dist"
- icon: paper-plane
  icon_pack: fas
  name: Docs
  url: "https://queelius.github.io/dfr_dist/articles/failure_rate.html"
tags:
- maximum-likelihood-estimation
- failure-rate
- hazard
- R-package
- R
- statistics
categories:
- statistics
- data-science
- inference
- survival-analysis
- reliability-analysis
---

Note: This is quite unfinished. I plan on fleshing this out when I revisit
my master's project work on estimating latent failure rate distributions. I
plan on generalizing the result to allow for arbitrary failure rates and to
use the MLE approach to estimate the parameters of the failure rate, since
the MLE approach is more general and can be used to estimate the parameters
given more complicated data generating processes.

The R packge `dfr_dist` provides an API for specifying and estimating dynamic failure rate distributions. They can depend on the data in any way, as the failure rate is any function of time and any set of predictors, as long as the failure rate satsifies two key properties:

1. It's non-negative. It is not meaningful to have a negative failure rate; the failure rate can decrease some times, and even go to $0$, though.
2. It's cumulative hazard has a limit of infinity, $\lim_{t \to \infty} H(t, x_1, \ldots, x_p) = \infty$. If this isn't satisfied, then the survival function is not well-defined.

This object satisfies all of the requirements of an [algebraic.dist](/project/algebraic-dist) and a [likelihoood model](/project/likelihood-model). It is also
designed to work well with the [algebraic.mle](/project/algebraic-mle) package,
which provides a framework for performing maximum likelihood estimation (MLE)
and retrieving various statistical properties of the MLEs.

