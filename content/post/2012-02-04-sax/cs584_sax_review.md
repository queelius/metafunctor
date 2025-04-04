---
title: "Review: A Symbolic Representation of Time Series, with Implications for Streaming Algorithms"
author: admin
date: '2021-10-30'
lastmod: '2021-10-30T08:18:32-05:00'
featured: no
share: true
profile: true
comments: true
tags:
- statistics
- time series
- computation
- symbolic
- data mining
categories:
- statistics
- data science
---

In [1], the authors present a method for constructing a symbolic (nominal) representation for real-valued time series data. A symbolic representation is desirable because then it becomes possible to use many of the effective algorithms that require symbolic representation, like hashing and Markov models.

The authors claim that one of the most useful time series operations is measuring the similarity between two time series data sets. To do this on the original time series, the Euclidean distance formula can be used. Therefore, for a time series transformation to be useful, distance measures applied to the corresponding transformations should provide some guaranteed lower bound on the `true` distance. This is a basic requirement for almost all time series algorithms in data mining. Non-symbolic transformations like Discrete Fourier Transform (DFT) and Piecewise Aggregate Approximation (PAA) models have this lower-bounding property. However, the authors claim no previously proposed symbolic representations do, which limits their usefulness.

Additionally, the authors observe that most raw time series data sets have very high dimensionality. This is problematic because time series mining algorithms are $\mathcal{O}(cn)$, where n is the number of dimensions. Therefore, preferably any transformations on the original time series will reduce the dimensionality to a more manageable size. Unfortunately, the authors observe, previously proposed symbolic representations preserve the original time series dimensionality.

Next, the authors present their symbolic representation, SAX (Symbolic Aggregate approXimation), which addresses each of the previously mentioned shortcomings of symbolic representations.

SAX is unique in that it uses an intermediate transformation, PAA, and then nominalizes the PAA representation into a sequence of characters'a string. By using the intermediate PAA representation, SAX enjoys two benefits:

1. It is able to exploit the dimensionality reducing properties of PAA, and

2. It provably lower bounds PAA, and PAA provably lower bounds the original time series. Therefore, through the transitivity relationship, SAX provably lower bounds the original time series.

Having the SAX representation offers many benefits in addition to those mentioned previously. For instance, when extracting subsequences of size $n$ from the original time series $T$, a sliding window approach can be used. However, this means $|T| - n + 1$ subsequences must be stored. When working with the SAX representation, however, the symbolic representation generalizes many subsequences such that many subsequences that were distinct in the original time series are identical in SAX. This allows one to compress the subsequence data using techniques like run-length encoding. This compressed form may be quite advantageous, especially if memory constraints are a factor.

To transform a time series into SAX, first the time series must be transformed into PAA. Next, an additional discretizing transformation is applied on the PAA. This is done in such a way as to make each discrete symbol appear with an equal probability in the string. This can be easily done if we observe that the original normalized time series has a normal distribution, therefore we simply need to partition the possible values in a time series into $a$ intervals ($a$ represents the number of discrete symbols in our alphabet--larger values of a correspond to more granular approximations), where the range of values in each interval has an equal probability of occurring (thus, for example, the interval containing the mean will likely be relatively small). Determining where the intervals begin and end can be easily done, since the time series has a normal distribution. Once the intervals are calculated, the PAA can be discretized into a string of symbols, $\{a, b, c, \ldots\}$, by assigning symbol $a$ to PAA coefficients that fall within the first interval, symbol $b$ to PAA coefficients that fall within the second interval, and so on until every PAA coefficient has been discretized. At this point, the original time series has been transformed into SAX.

To show that the SAX distance measures can be made to lower bound the original time series, the authors first demonstrate distance measure for the PAA, which is similar to the Euclidean distance formula:

$$
    \operatorname{DR}(\bar{Q},\bar{C}) = \sqrt{\frac{n}{w} \sum_{i=1}^w (\bar{q}_i-\bar{c}_i )^2}.
$$

$\operatorname{DR}$ lower bounds the Euclidean distance formula for the original time series, where $\bar{Q}$ and $\bar{C}$ represent the PAA transformation of time series $Q$ and $C$. With this formula in mind, next they present the distance formula for SAX:

$$
    \operatorname{MIN\_DIST}(\bar{Q},\bar{C})=\sqrt{\frac{n}{w} \sum_{i=1}^w (\operatorname{dist}(\hat{q}_i-\hat{c}_i))^2}
$$

The authors observe that the above two formulas are identical, except for the presence of the inner `dist` function. Since $\operatorname{dist}(\hat{q} - \hat{c})$ lower-bounds $\bar{q}_i-\bar{c}_i$, $\operatorname{MIN\_DIST}$ must lower bound $\operatorname{DR}$. And, by transitivity relation, $\operatorname{MIN\_DIST}$ therefore lower-bounds the distance measure for the original time series.

The authors then go on to show how the dist function can be a fast table lookup, where the table is the cross product of the symbols $\{a, b, \ldots\}$. Each cell in the table represents the distance between the row symbol and the column symbol, and its value can be calculated with a straightforward formula.

Next, the authors conduct a number of experiments to empirically test the validity and performance of SAX when compared to the traditional Euclidean distance formula (on the original time series data) and other representations.

For instance, they show the experimental results generated with hierarchical clustering. The results reveal that the clusters generated from the SAX representation (using MINDIST as the distance measure) are the same as the clusters generated from the original time series (using the classical Euclidean distance formula). Other symbolic representations performed poor by comparison, and the authors stipulate that the superior performance of SAX can be attributed to the smoothing effect caused by dimensionality reduction.

They discuss the results of other experiments as well. In each case, SAX performs comparatively well, and in some cases it does better than the Euclidian distance measure on the original time series.

Next, the authors demonstrate the advantages of having a symbolic representation of a time series. For instance, motif discovery�looking for subsequences with the same general pattern�can be accomplished by hashing (which requires discrete data) subsequences into buckets using a random subset of the subsequence as the key, then searching through the buckets. Note that since SAX provides a symbolic representation, it tends to be resilient to noise (it is not over-fitted to the noise) because the discrete values smooth out most of the variations caused by noise.

Finally, the authors present their conclusion. They emphasize that their experimental results demonstrate that SAX is competitive or superior to other approaches on a variety of classification and clustering problems. Furthermore, they point out that the symbolic nature of SAX opens it up to other domains, like motif discovery which cannot be done on real-valued data. They close with a few remarks on future directions to pursue with SAX; notably, they are curious to see how well other data mining algorithms (that require discrete representations) do when paired with SAX.

# Analysis and Critique

The symbolic nature of SAX opens up the possibility of using many fast and efficient algorithms and data structures (that require discrete representations) developed in other disciplines which are unavailable to real-valued representations, like PAA or FFT. However, SAX does so while still providing a lower bound on the distance measures of the original time series, unlike other symbolic representations discussed in the paper. This suggests that SAX is offering a reasonable approximation (to whatever degree is required) of the original time series, which gives us confidence in its results. SAX allows us to use its simplified, dimensionally reduced transformation in memory constrained environments, reasonably confident that the results acquired on its reduced data set will be applicable to the larger, original data set. Since time series data sets tend to be quite large, this advantage should not be overlooked.

However, I would argue the more important properties of SAX have more to do with its symbolic nature and less to do with its dimensionally reduced form. Since it offers an �accurate� (according to the MINDIST lower bounding property) symbolic representation, it is able to effectively generalize the time series data points, permitting more sophisticated analysis of time series that is resilient to noise. For instance, the motif discovery algorithm, PROJECTION [2], is quite promising, and has applicability to a wide range of domains. This algorithm is only possible if the time series has been discretized as required by the hashing function.

Unfortunately, in parts the paper was more difficult to read than it needed to be. This is, of course, not uncommon. While I feel I was able to understand their work, at times I had to take a second, third, or even fourth look at the text to adequately grasp the material presented. In some cases, simple fixes like providing better examples would have sufficed. For instance, in their paper, much of the discussion in Section 3 was about how to construct a symbolic representation of a time series. For much of the discussion, they used an alpha size of three (such that there were only three possible values that a data point could have, a, b, or c). However, the first time they illustrate the dist table (Table 4) for facilitating �distance� measures between symbolic pairs, they use an alphabet of size four. In particular, Figure 5 used an alphabet of size three, and it would have been convenient to be able to compare it to Table 4.

Also, they do not adequately address many of the topics mentioned in their abstract and introduction. For instance, they mention streaming in the title of their paper, and throughout the abstract, but then barely mention it again throughout the remainder of the paper. I strongly believe that SAX is amenable to streaming algorithms, but the authors could have did a better job at demonstrating this.

# Conclusion 
Overall, despite my criticisms, the paper effectively conveys the main thrust of their accomplishment, SAX, and how it can be put to use. Furthermore, the authors seem to be addressing a rather urgent need (in 2003 and even today), especially in light of the exponentially growing volume of time series data being generated. As the saying goes, we are increasingly data rich but information poor. Thus, we need more effective ways to analyze and find patterns in these huge data sets. The authors of [1] make a convincing case that SAX will help us towards this end.

# References

[1] Ramakrishnan Srikant and Rakesh Agrawal. A Symbolic Representation of Time Series, with Implications for Streaming Algorithms. In Proceedings of the 8th ACM SIGMOD workshop on Research issues in data mining and knowledge discovery (2003), pp. 2-11.

[2] Tompa, M. & Buhler, J. (2001). Finding Motifs Using Random Projections. In proceedings of the 5th Intel Conference on Computational Molecular Biology. Montreal, Canada, Apr 22-25. pp. 67-74.
