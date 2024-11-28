---
title: "Reliability Estimation in Series Systems"
subtitle: "Maximum Likelihood Techniques for Right-Censored and Masked Failure Data"
author: "Alex Towell"
email: lex@metafuctor.com
summary: "A simulation study is used to assess the sensitivity of the MLE to various parameters like sample size, masking probability, and other so on, using components with Weibull lifetimes arranged in series."
date: "2023-03-29"
links:
- name: GitHub
  url: "https://github.com/queelius/reliability-estimation-in-series-systems"
- name: PDF
  url: "/alex-towell-math-proj-masters.pdf"
tags:
- maximum-likelihood-estimation
- data-generating-process
- statistics
- siue
- data-science
- inference
- likelihood-models
---

The repo on [Github](https://github.com/queelius/reliability-estimation-in-series-systems) contains the code and data for my master's project in mathematics and statistics at SIUe. The project is titled "Reliability Estimation in Series Systems: Maximum Likelihood Techniques for Right-Censored and Masked Failure Data".

The paper citation page is [here](/publication/math-proj).

You can view the PDF [here](/alex-towell-math-proj-masters.pdf). The abstract is as follows:

> This paper investigates maximum likelihood techniques to estimate component
> reliability from masked failure data in series systems. A likelihood model
> accounts for right-censoring and candidate sets indicative of masked failure
> causes. Extensive simulation studies assess the accuracy and precision of
> maximum likelihood estimates under varying sample size, masking probability,
> and right-censoring time for components with Weibull lifetimes. The studies
> specifically examine the accuracy and precision of estimates, along with the
> coverage probability and width of BCa confidence intervals. Despite
> significant masking and censoring, the maximum likelihood estimator
> demonstrates good overall performance. The bootstrap yields correctly
> specified confidence intervals even for small sample sizes. Together, the
> modeling framework and simulation studies provide rigorous validation of
> statistical learning from masked reliability data.

It has a companion R package, `wei.series.md.c1.c2.c3`, which is a narrow library I developed in support of my master's project at SIUe in mathematics and statistics. It provides a set of functions for fitting Weibull series systems with masked failure data.
See the [GitHub repo](https://github.com/queelius/wei.series.md.c1.c2.c3) for more.

I'm working on several spinoff papers. See the <a href="/alex-towell-math-proj-masters.pdf#page=31">Future Work</a>
section in the paper for more details.

Shoot me an email at [lex@metafunctor.com](mailto:lex@metafunctor.com) if you're interested in collaborating on any projects.