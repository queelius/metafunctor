---
title: 'R package: md.tools'
author: admin
summary: "An API for constructing and working with likelihood models; works well with `algebraic.mle` R package"
date: '2022-05-13'
external_url: "https://github.com/queelius/md.tools"
image:
  caption: Tools for masked data
  focal_point: Smart
  preview_only: yes
links:
- icon: github
  icon_pack: fab
  name: Code
  url: "https://github.com/queelius/md.tools"
- icon: paper-plane
  icon_pack: fas
  name: Docs
  url: "https://queelius.github.io/md.tools"
featured: no
tags:
- masked-data
- latent-data
- R-package
- R
- statistics
categories:
- statistics
- data-science
- survival-analysis
- reliability-analysis
---

The R package `md.tools` is a miscellaneous set of tools for working with
*masked data* and common features of masked data. If doing simulation studies,
it also supports latent data, i.e., data that is not observed but is generated,
and various other features that may be useful for simulation studies.

The tool set takes inspiration from functional programming, with inputs and
outputs defined over masked data frames of type `tbl_md` (or just data frames),
making it consistent with the *tidyverse* way of doing things.