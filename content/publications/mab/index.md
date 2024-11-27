---
title: Estimating How Confidential Encrypted Searches Are Using Moving Average Bootstrap Method
abstract: This paper applies an approach of resilience engineering in studying how effective encrypted searches will be. One of the concerns on encrypted searches is frequency attacks. In frequency attacks, adversaries guess the meaning of the encrypted words by observing a large number of encrypted words in search queries and mapping the encrypted words to guessed plain text words using their known histogram. Thus, it is important for defenders to know how many encrypted words adversaries need to observe before they correctly guess the encrypted words. However, doing so takes long time for defenders because of the large volume of the encrypted words involved. We developed and evaluated Moving Average Bootstrap (MAB) method for estimating the number of encrypted words (N*) an adversary needs to observe before an adversary correctly guesses a certain percentage of the observed words with a certain confidence. Our experiments indicate that MAB method lets defenders to estimate N* using only 5% of the time, compared to the cases without MAB. Because of the significant reduction in the required time for estimating N*, MAB will contribute to the safety in encrypted searches.
authors:
- name: Alex Towell
  email: lex@metafunctor.com
- name: Hiroshi Fujinoki
  email: hfujinoki@siue.edu
doi: ""
links:
- name: IEEE Xplore
  url: https://ieeexplore.ieee.org/document/7830707
- name: GitHub
  url: https://github.com/queelius/estimating_es_conf_moving_avg_bootstrap/
- name: PDF
  url: '/uploads/07830707.pdf'
date: "2017-01-01T00:00:00Z"
tags:
- encrypted search
- bootstrap
- moving average
- frequency attack
- security
- confidentiality
---



