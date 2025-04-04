---
title: Rank-orderd Encrypted Search 
author: admin
date: '2021-06-17'
featured: no
draft: yes
comments: yes
slug: rank-ordered-encrypted-search
categories:
- Encrypted search
- Rank-ordered search
- Set-theoretic search
- Oblivious data type
- Bernoulli model
image:
  caption: ''
  focal_point: ''
  preview_only: yes
projects: []
---

# Rank-ordered Encrypted Search

Consider a map from a set of keys to a set of values, where the keys are the search keys
or relations between search keys, and the values are the information about the search keys which may be used to compute rank-ordering, such as frequency or proximity information.

**Encrypted search** enables untrusted systems to obliviously search documents on behalf of authorized users, where the search query, the confidential documents being searched over, and the search results are oblivious values.

Oblivious values are values that do not reveal any information about the underlying plaintext values, such as an oblivoius Boolean value which may
be true or false (or neither, in the case of noisy oblivious values).
These requirements may not be fully met in most cases. For example, the search results may reveal the number of search keys in the query (although this too can be mitigated). However,
the goal is to minimize the amount of information that is revealed to an untrusted system
that is performing the search.

One of the earlier papers presented on Encrypted search observed that individuals may wish to outsource storage logistics to cloud storage providers but do not trust the providers with confidentiality requirements (Son00). To enable an untrusted system to search a confidential document on an authorized user's behalf, the untrusted system must have some sort searchable representation of the document, which we call a *secure index*.

In *rank-ordered* Encrypted Search, a confidential document has a *degree-of-relevance* to a hidden query. The degree-of-relevance can be based off many criteria, such as the *multiplicity* or *proximity* of the search keys in the document. Thus, to facilitate rank-ordered search, we need a data structure that is able to store *arbitrary* information about the search keys in a document.

In what follows, we consider secure indexes represented by an oblivious entropy map, which
permits nearly any kind of search, including Boolean search, rank-ordered search, set-theoretic search, proxmity search, and so on. As a special case, we consider secure indexes represented by an oblivious set, which permits only set-theoretic Boolean search.

## Oblivious entropy map: theory and implementation

The basic theory behind an entropy map is to map values in the domain to values in
the codomain by *hashing* to a prefix-free code in the codomain. We do not store
anything related to the domain, since we are simply hashing them, and a prefix
of that hash will be used as a code for a value in the codomain.

We actually allow for many different codes for each value in the codomain, so that,
for instance, a code for, say, the value `a` may be `00`, `01`, `10`, and `11`.
Notice that we can efficiently decode this as `a` if the hash is less than 4.

Suppose $\Pr\{f(X) = y\} = p_y$, $X \sim p_X$, then the optimally space-efficient code,
assuming a uniform hash function $h$, is to assign prefix-free codes for $y$ whose
probability of being mapped to by $h$ sums to $p_y$. In this case, the expected
bit length is given by
$$
    \ell = -\sum_{y \in \mathcal{Y}} p_y \log_2 p_y,
$$
which if we imagine sampling $x$ from $\mathcal{X}$ with $p_X$ and then mapping to
$y = f(x)$ and observing the sequence of $y$'s, then the expected bit length is
the entropy of the sequence of $y$'s. This is why we call it an entroy map.

If $\mathcal{X}$ is finite, then we can just imagine implicitly encoding the domain
and then for each value in the domain, storing the prefix-free code that it maps to,
which has an average bit length of $\ell$$ and
a total bit length of $|X| \ell$.

We can allow rate distrotion, too, by failing to code for some of the elements
properly. For instance, a popular choice is when one of the values, say $y'$, is
*extremely* common such that, for instance, $p_{y'} > .99$, then we can give it a
prefix-free code that sums to $p_{y'}$ and then *not* code for it in the entropy map,
in which case it will, for some randomly selected $x \in \mathcal{X}$, be mapped to
$y'$ with probability $p_{y'}$ (which is sufficiently large, and can be made as
close to $1$ as desired if we wish to trade space for accuracy), and then for the
remaining values in the domain, code for them correctly (or also allow errors on
them, too, but only after trying to find correct codes for each of them).

For instance, suppose we have a set-indicator function $1_{\mathcal{A}} : \mathcal{X} \to \{0,1\}$, where $\mathcal{A} \subseteq \mathcal{X}$, and $\mathcal{X}$ is a very large set,
then we may seek to give $0$ a set of prefix codes that sums to $p_0 = 1-\varepsilon > .99$,
and then only find codes for $1$ in $\mathcal{A}$, which has a probability of
$p_1 = 1 - p_0 = \varepsilon \leq .01$. Then, for any $x \in \mathcal{X}$, we can
test if $1_{\mathcal{A}}(x) = 1$ by testing if $h(x)$ is a code for $0$ or $1$, and
if it is a code for $0$, then we know that it is definitely not a member of $\mathcal{A}$,
but if it is a code for $1$, then it is a member of $\mathcal{A}$ with a false positive
rate of $\varepsilon$ and a true positive rate $1$, since a randomly drawn element
not in $\mathcal{A}$ will map to $0$ with probability $1 - p_0 = \varepsilon$ and
any element in $\mathcal{A}$ will map to $1$ with probability $1$ (since we
explicitly code for it). In this formulation, we can think of the entropy map as
rate-distorted approximation of a set-indicator function, which we refer to as a
Bernoulli set-indicator function (or more generally a Bernoulli map over `X -> bool`).

The Bloom filter is an entropy map in which, implicitly, the probability of a negative
mebership test is $\varepsilon$. The Bloom filter is a special case of the entropy
map over set-indicator functions.



The *oblivious* entropy map is just an entropy map where the hash function is
applied to trapdoors of $\mathcal{X}$ and the prefix-free codes for $\mathcal{Y}$
have no rythm or reason to them.

## Oblivious set: Boolean search
In *Boolean* Encrypted Search, a confidential document is *relevant* to a hidden query
if the secure index of the document contains every trapdoor search key in the hidden
query. That is, the secure index may be modeled as an *oblivous set*.

Sets enable queries about the presence of a trapdoor. Let
```cpp
contains : (secure_index, trapdoor) -> bool
```
be a set-membership test (set-indicator function), e.g., `contains(S,x)` tests if
trapdoor `x` (which may be, say, a trapdoor of unigram word, bigram word, or
something else, like a stemmed phrase) is a member of secure index `S`.

This can be used to implement Boolean search and set-theoretic queries, such as
intersection, union, and difference. However, it is not sufficient for rank-ordered
search, which requires more information about the relation the query (of trapdoors) has
to the set of documents (secure indexes).

## Oblivious multi-sets
Multi-sets, which enable queries about the multiplicity of a trapdoor, enable
rank-ordered searches that consider the frequency of words, such as BM25.

In this case, we need a data structure that is able to store the multiplicity of
search keys in a document. This is enabled by an *oblivious multi-set* where the
keys are the search keys and the values are the multiplicity of the search keys.
The expected bit length is given by
$$
    \mathcal{O}(-\log_2 \varepsilon + \log_2 q_{\rm{max}})
$$

$$
    \log_2 \frac{f_{\rm{max}} - f_{\rm{min}} + 1}{\varepsilon} 
$$
bits per trapdoor if we assume that the frequency of a word in a document is *uniformly* distributed between $f_{\rm{min}}$ and $f_{\rm{max}}$.

Let
```cpp
contains : (secure_index, unigram) -> bool
```
be a set-membership test (set-indicator function), e.g., `contains(S,x)` tests if
unigram $x$ is a member of secure index $s$. This is the bare minimum and only
supports Boolean search.



$\Multiplicty(s \colon \SecureIndex, x \colon \Unigram) \mapsto \mathbb{Z}$. Count the multiplicty.

### Secure postings list
Uses the variable-length perfect map filter. Keys are unigrams. Maps to an array element. The first bit $1$ indicates the initial position and the gaps are encoded in the rest of the bits in the element. If this info can't fit, then the rest of the bits are a pointer into a larger bit string where the first bit sequence (universal coder?) indicates starting position, then 


- `contains(s : secure_index, x : unigram) -> bool`. Test unigram $x$ (a bit string) for membership in secure index $s$. This is the bare minimum and only supports Boolean search.
    \item [-] $\Multiplicty(s \colon \SecureIndex, x \colon \Unigram) \mapsto \mathbb{Z}$. Count the multiplicty of unigram $x$ in secure index $s$.
    
    \item [-] $\Locations(s \colon \SecureIndex, x \colon \Unigram)$. Retrieve the locations of unigram $x$ in secure index $s$.
    \item [-] $\ContainsSequence(s, x_1 x_2 \cdots x_k)$. Test ordered $k$-gram $x_1 x_2 \cdots x_k$ for membership in secure index $s$. If a \gls{biword} model is used, then $\Contains$ may be used to implement $\ContainsSequence$.
    \item [-] $\LocationsSequence(s, x_1 x_2 \cdots x_k)$. Retrieve the locations of $k$-gram $x_1 x_2 \cdots x_k$ in secure index $s$.
\end{enumerate}
These interface elements may be used to implement other interfaces, like rank-ordered search (e.g., \gls{bm25} or \gls{mindistx}) or set-theoretic queries.\footnote{Note that if the $\Locations$ interface is supported, then $\MinimumDistance$ and $\Multiplicity$ with respect to $\Locations$.}

### Secure rank
The secure index does not disclose which *plaintext* words are contained in a document, nor any information attached to the *plaintext* word, until a *hidden query* is submitted against the document. However, at the point of submission, while the *plaintext* word is not necessarily disclosed, the information attached to the word is.

Such information may be sufficient to allow the adversary to estimate (with a probability of success greater than random chance) the *plaintext* word that corresponds to a *trapdoor* in a hidden query. Depending upon the nature of the information, the adversary may be able to estimate other characteristics about the confidential document. In the case of a secure postings list, assuming the postings have minimal error, the adversary may be able to estimate much of the *plaintext* of an entire confidential document.

One way to combat this technique is to precompute the results of queries (perhaps just reasonably plausible queries) with respect to a given confidential document. Then, the untrusted system does not need to know any information about particular atomic words, but rather, it needs to know information about how a confidential document is relevant to particular queries.

Suppose the query model is a *bag-of-words* and the maximum number of words in a query is $p$. As a bag-of-words, the order of the words in the query is irrelevant. Since a document may only contain some of the words in the query, we must code all $2^p-1$ subsets (excluding the empty set).

Given a bag-of-words query, we could *canonically* order the bag and map this to any desired ranking. That is, we *precompute* the ranking. This can have a high up-front cost and a high storage cost.

If the document consists of $k$ unique words, then there are
$$
    \sum_{j=1}^{p} \binom{k}{j}
$$
canonical keys. If $p$ is unbounded, then there are
$$
    2^k - 1 
$$
canonical keys, which is impractical for most documents with even a relatively small number of unique words, e.g., if $k=30$, then there are approximately a billion keys.

In either case, the expected lower-bound on bit length for the oblivious map is given by
$$
    -\sum_{j=1}^{p} \binom{k}{j}\left(\log_2 \varepsilon + \mu\right)\,,
$$
where $\mu$ is the average bit length of the ranking coder which represents some rank-ordered search metric, like BM25 or MinDist, or the *proximity* of the words for Boolean proximity searches (or multiple such metrics may be coded).

Suppose the maximum query is $p=3$, then the map has on the order of
$$
    \mathcal{O}(k^3)
$$
keys and the oblivious map has a lower-bound on the order of
$$
    -\mathcal{O}\left(k^3\right) \left( \log_2 \varepsilon + \mu \right) \; \si{bits}
$$
or
$$
    -\mathcal{O}\left(k^2\right) \left( \log_2 \varepsilon + \mu \right) \; \si{bits \per search key}
$$

If $k \approx 100$, which may be a reasonably large document (especially if *common words* are removed and *stemming* is used to normalize words), then the lower-bound is around
$$
    -10^4 \log_2 \varepsilon + \mu \; \si{bits \per search key}\,.
$$


## Rank-ordered search
Rank-ordered information retrieval provides a mapping between queries and degree-of-relevancy for each document in a collection.

In **information retrieval** (IR), finding effective ways to measure relevancy is of fundamental importance. To that end, many algorithms and heuristics have been devised to rank-order documents by their estimated relevancy to a given query. We explore term weighting and term proximity weighting heuristics in the context of secure indexes.

\subsection{Precision and recall}
Precision and recall are relevant metrics for Boolean searches; they do not rank retrieved documents like \gls{bm25} or \gls{mindistx}; a document is either considered relevant (contains all of the terms in a query) or non-relevant.

Precision measures the proportion of retrieved documents that are relevant to the query. It is defined as:

???

Precision has a range of $[0, 1]$. Recall measures the proportion of relevant documents that were retrieved. It is defined as:

???

Recall also has a range in $  \left[ 0, 1 \right]  $. It is trivial to achieve a recall of $ 1  \left( 100 \%  \right)  $ by retrieving every document in the corpus. However, this comes at the cost of decreased precision. Thus, in general there is a trade-off between precision and recall.

\subsection{Mean average precision}
Mean average precision is a popular way to measure the performance of information retrieval systems with respect to degree of relevancy, however relevancy may be defined. In particular, we use \gls{map} to measuring how closely the rank-ordered output (of either \gls{bm25} or \gls{mindistx}) of the secure indexes matches the rank-ordered output of non-secure, \glspl{canonicalindex} which provide perfect location and frequency information.

The more approximately a secure index represents a document, the less information one can infer about the document from the secure index. Thus, to what extent the secure index can approximate a document while still achieving a reasonable \gls{map} score is an important question.

\Gls{map} is calculated by taking the mean of the \glspl{avgprecision} of $n$ queries. The \gls{precision} at $k$ is given by
\begin{equation}
\begin{split}
    \rm{precision}(q, k) &=\\
    & \mkern-36mu
    \frac{
    \lVert
        \{\text{$k$ most relevant docs to query $q$}\}
        \cap
        \{\text{top $k$ retrieved docs for query $q$}\}
    \rVert}{k}.
\end{split}
\end{equation}

???

The average precision for the top $n$ documents is given by
\begin{equation}
    \rm{avgprecision} \left( q,n \right) =\frac{1}{n} \sum _{k=1}^{n} \rm{precision} \left( q,k \right).
\end{equation}

???

The *map* for the top $n$ documents over a query set $Q$ is given by
%\begin{equation}
%    \mapfn \left( Q,n \right) = \frac{1}{ \cardinality{Q}} \sum_{i=1}^{\cardinality{Q}} \rm{avgprecision} \left( q_i, n \right).
%\end{equation}

Given a set of documents $D$ and a query $Q$, to estimate a document’s \gls{map} score, we need a set of documents $D$ and a query set $Q$. Then, we construct a set of secure indexes $ SI $ for $ D $, and rank-order both $ SI $ and $ D $ according to each $ q \in Q $. Finally, we calculate the \gls{map} over these rank-ordered outputs using the rank-ordered outputs for $ D $ as the $true$, canonical output.

### Example
Consider the following. Suppose the rank-ordered list of relevant documents to a query is $(3, 0, 1, 2, 4)$, and the retrieved ranked-ordered list (by a secure index) is $(2, 4, 3, 0, 1)$.
The precision at $k=1$ is given by
\begin{equation*}
    \frac{|\{ 3 \} \cap \{ 2 \}|}{1}=\frac{|\emptyset|}{1}=0\,,
\end{equation*}
the precision at $k=2$ is given by
$$
    \frac{|\{ 3,0 \} \cap \{ 2,4 \}|}{2}=\frac{|\emptyset|}{2}=0\,.
$$
the precision at $ k=3 $ is given by 
$$
    \frac{|\{ 3,0,1 \} \cap \{ 2,4,3 \}|}{3}=\frac{|\{ 3 \}|}{3}=\frac{1}{3}\,,
$$
the precision at $k=4$ is given by
$$
    \frac{|\{ 3,0,1,2 \} \cap \{ 2,4,3,0 \}|}{4}=\frac{|\{ 3,0,1 \}|}{4}=\frac{3}{4}\,,
$$
and the precision at $k=5$ is given by
$$
    \frac{|\{ 3,0,1,2,4 \} \cap \{ 2,4,3,0,1 \}|}{5}=\frac{|\{ 3,0,1,2,4 \}|}{5}=\frac{5}{5}=1\,.
$$
Thus, the average precision is given by
$$
    \frac{0+0+\frac{1}{3}+\frac{3}{4}+1}{5}=\frac{5}{12}\,.
$$
The ma} would simply be the mean of the average precisions for $ M $ queries.

Note that the average precision for the last value of $ k $ is necessarily $ 1 $ if, by that iteration of $ k $, the relevant set and the retrieved set contain the same elements. However, in general, this is not the case; for instance, if the relevant ranked list of documents to a query is $(A, B)$, and the retrieved ranked list is $(D, C, B, A)$, then if the mean average precision goes from $ k=1 $ to $ k=2 $, the average precision is $0$. In our simulations, we do a
variation of this.

Suppose the relevant ranked list of documents to a query is $(A=0.9, B=0.85, C=0, D=0)$, and the retrieved rank-ordered list is $(A=0.9, C=0.85, D=0.5, B=0)$. Then, I calculate the average precision for the top $ k=3 $ instead of the top $ k=4 $ or top $ k=2 $. In this example, document $B$ is not included in any of the precision at $k=1$ to $k=3$ calculations.
 
Finally, in one of the experiments, we conduct a “page one” \gls{map} test, i.e., we find the mean average precision using only the top $10$ results. The randomized algorithm does much more poorly in this instance, e.g., with over $ 85 \%  $ probability, the mean average precision will be less than $ 0.05 $.

## Term importance: BM-25
BM-25, a well-established *tf-idf* variant, is one of the relevancy measures chosen for our experiments. Mathematically:

???

where `avgdl` is the average size (in words) of documents in $ D $.
 
Parameters $ b $ and $ k_{1} $ are free parameters. In the experiments, they are set to typical values ; $ b $ is set to $ 0.75 $ and $ k_{1} $ is set to $ 1.2 $. Ideally these parameters would be automatically tuned for each secure index. The function $ tf $ stands for term frequency and simply returns the number of occurrences of term $ t $ in document $ d $. The function $ idf $ stands for inverse document frequency.

????

where $  \vert D \vert  $ is the number of documents in $ D $ and $ count $ is a function which returns the number of documents in $ D $ which had one or more occurrences of term $ t $.

When using BM25 to rank search results, each document $ d $ in $ D $ is ranked according to query $ Q $
 by the function $ BM25Score \left( d,Q \right) = \sum _{t \in Q}^{}BM25 \left( d,t,D \right)  $
, where $ t $ is a term in query $ Q $. After giving every document a BM25 rank, the results are sorted in descending order of rank as the final output.

Note that BM25Score is not used in most real-world information retrieval systems; instead, they typically employ a vector space model, in which a vector $ v \left( d \right)  $ for each document $ d $ in $ D $
 is constructed, where each dimension represents a word $ t $ with a weight equal to $ BM25 \left( d,t,D \right)  $. Subsequently, a document can be ranked according to a query by taking the cosine similarity of their respective vectors. However, as explained on page 12, this representation is problematic in light of the limited information (for confidentiality) available in secure indexes.
 
\subsection{Term proximity: \gls{mindistx}}
\Gls{mindist}, like \gls{bm25}, ranks documents according to their proximity relevance to a given query. It is a less established ranking heuristic than \gls{bm25}, but in experiments it had performed well compared to other proximity heuristics. In our experiments, we add additional tunable parameters to \gls{mindist} and call it \gls{mindistx}.

\Gls{mindistx} is a proximity heuristic in which the minimum distance between each existent pair of terms are summed over.
\begin{example}
\label{ex:mindistx_sum}
Consider a document
\begin{equation*}
    \vec{d} = \left( A, B, D, D, A, D, C \right)
\end{equation*}
and a query
\begin{equation*}
    \vec{q} = \{ A, B, C \}
\end{equation*}
where $A$, $B$, $C$, and $D$ are searchable terms. Then, the minimum pairwise distances are given by
\begin{align*}
    d(A, B) = 1\,,\\
    d(A, C) = 2\,,\\
    d(B, C) = 5\,,
\end{align*}
and the summation of these distances is simply
$s = 1 + 2 + 5 = 8$.
\end{example}
\Gls{mindistx} is a scoring function dependent upon the summation of the minimum pairwise distances, as give by $s$ in \Cref{ex:mindistx_sum}. Thus, \gls{mindistx} requires that a secure index represent distances between terms. The most obvious way to accomplish this is by storing approximation location information.

The more concentrated the query terms in a document are, the more relevant the document is to the query. However, this is true only up to a certain point.
\begin{example}
Consider a query
\begin{equation*}
    \vec{q} = \{\text{computer}, \text{science} \}
\end{equation*}
and two documents $A$ and $B$, where $A$ contains both *computer* and *science* on page $7$ and $B$ contains *computer* on page $7$ and *science* on the page $20$. It is obvious that $A$ should be considered more relevant to $\vec{q}$ since the two keywords of interest are much closer together.

However, consider a third document $C$ that contains *computer* on page $7$ and *science* on page $100$. This is not much worse than $B$; both documents are not that relevant and $B$ is only marginally more relevant at best.
\end{example}

\Gls{mindistx} has the following functional form.
\begin{definition}
Let $\mathbb{Q}$ be the set of query terms, $\mathbb{Q'}$ be the subset of $\mathbb{Q}$ that exist in the given document $\vec{d}$, and $s$ be the sum of the minimum pairwise distances between terms in $\mathbb{Q^{*}}$.
\begin{equation}
    \mindistx\left(s, \cardinality{\mathbb{Q^*}} \given \alpha, \gamma, \beta, \theta\right) =
        \ln \left(
            \alpha + \gamma  \cdot \exp \left\{ -\frac{ \beta s}{ \left\vert \mathbb{Q^{*}} \right\vert ^{\theta}} \right\}
        \right)\,,
\end{equation}
where $\alpha , \gamma , \beta , \theta, s > 0$.
\end{definition}

To determine if $\mindistx$ matches the intuition--a strictly decreasing function that flattens out as $s$ increases--it may be instructive to consider the limits. Asymptotically, as $s \to 0$,
\begin{equation}
    \mindistx(s, \cardinality{\mathbb{Q^*}} \given \cdot) \to \ln \left(  \alpha + \gamma  \right)
\end{equation}
and as $s \to \infty$,
\begin{equation}
    \mindistx(s, \cardinality{\mathbb{Q^*}} \given \cdot) \to \ln\alpha\,.
\end{equation}
To determine if these end points are the maximum and minimum values respectively, let us consider the partial derivative
\begin{equation}
    \frac{\partial{}}{\partial s} \mindistx(s, \cardinality{\mathbb{Q^*}} \given \alpha, \beta, \gamma) =
        -\cardinality{\mathbb{Q^{*}}}^{-\theta}
        \frac
            { \beta \gamma \exp(-p) }
            {\alpha + \gamma \exp(-p)}
\end{equation}
where
\begin{equation*}
    p = \frac{\beta s}{\cardinality{\mathbb{Q^{*}}}^\theta}\,.
\end{equation*}

This partial, for all supported values, is negative. As $s \to 0$, 
\begin{equation}
    -\frac{1}{ \vert Q^{'} \vert ^{ \theta }} \left( \frac{ \beta  \gamma }{ \alpha + \gamma } \right)\,,
\end{equation}
and as $s \to \infty$ the partial approaches $0$. This matches the desired intuition; for small $s$, a small increase in $s$ corresponds to a large decrease in $\mindistx$; and, for large $s$, a small increase in $s$ corresponds to small decrease in $s$.
 
Note that for large $\cardinality{Q^{*}}$, $\mindistx$ function decreases less rapidly than for small $\cardinality{Q^{*}}$, which is the desired behavior. Recall that $\cardinality{Q^{*}}$ corresponds to the number of query terms matched in the document. We do not wish to penalize (at least not too harshly) a document that contains more of the query’s terms but spread out over a larger region compared to a document that contains fewer of the query's terms but spread out less.

\gls{mindistx} may be used as a way to add proximity sensitivity to already established scoring methods, like \gls{bm25}. For example, a linear combination of their scores can be used as the final output of a scoring function that is both sensitive to proximity and term frequencies:

\begin{equation}
    \Score(d, Q, D) = \lambda \mindistx + (1 - \lambda) \bmscore(d, q, D)
\end{equation}

## Uncertainty vs mean average precision
In Encrypted Boolean Search (EBS), the only information disclosed to the untrusted system about a collection of confidential objects is which objects are relevant to obfuscated search keys (trapdoors of search keys).

To facilitate Encrypted Rank-Ordered Search (ERS), more information about the confidential object is required to compute a *degree-of-relevance*.

A lot of error or uncertainty is acceptable if the ranked-ordered search operations are still sufficiently useful.

False positives (approximate sets and approximate maps) is one kind of error. We also want to artificially inject other kinds of errors or approximations.

