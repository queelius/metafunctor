---
title: Working memory as an inductive bias
author: admin
date: '2023-06-17'
featured: no
comments: yes
slug: working-memory-as-an-inductive-bias
categories:
  - machine-learning
tags:
  - Cognitive Science
  - Machine Learning
  - LLM
  - Regularization
draft: no
image:
  caption: ''
  focal_point: ''
  preview_only: yes
---

This blog post is from a chat I had with a ChatGPT,
which can be found [here](https://chat.openai.com/share/f298898b-9787-48f4-8959-c8cd04eb98b4)
and [here](https://chat.openai.com/share/0d33ab33-0664-4b25-b1c2-22864b28db48).

> I'm not sure if this is a good blog post, but I'm posting it anyway. It's remarkable
> how quickly you can slap stuff like this together, and I'm not sure this is
> saying anything valuable, particularly since it only required a bit of prompting
> from me.

***

<img src="./featured.png" style="width: 200px; float: left; margin: 10px;">

## Working memory as an inductive bias

Human cognitive abilities, while remarkable, are bounded. Our working memory can effectively hold and process only a limited amount of information at once. Cognitive psychology often references the "magic number seven", suggesting that most adults can hold between five and nine items in their working memory. This constraint necessitates the use of abstractions in order to understand complex systems.

Consider a situation where we're dealing with multiple variables $(x_1, x_2, x_3, x_4)$. Our brain might struggle to simultaneously process the joint distribution of these variables due to the limitation of our working memory. However, if we create an abstraction where $X$ represents $(x_1, x_2)$ and $Y$ represents $(x_3, x_4)$, we simplify the cognitive task to handling the joint distribution of just two variables $(X,Y)$, which is a more manageable task.

But here’s the rub: In condensing reality to fit our cognitive capacities, we risk losing vital information. For instance, a critical relationship (that may only be critical in a certain context) between $x_2$ and $x_4$ might be discarded in our new model. This often leads to the situation where "the whole is greater than the sum of the parts." In other words, the full, nuanced understanding of the system may be irreducible, with important aspects of its behaviour emerging only when all variables are considered together. Such emergent phenomena represent a key challenge in working with abstractions.


## Working Memory and Inductive Bias

Our small working memory influences our reasoning abilities and shapes our understanding of the world around us, in effect serving as an inductive bias. It's like a filter, shaping the patterns we detect and the generalizations we form based on the information we encounter.

This constraint, however, might not be entirely detrimental. It could even be an advantage, given the regularities in our reality. Think of it as a form of regularization in machine learning, where constraints prevent the model from overfitting the training data, thereby improving generalization to unseen instances. If we had much larger working memories, we might be prone to overfitting to our past observations, impairing our ability to adapt and survive in new situations, particularly those on the 'long tail of the distribution'. Our survival, after all, depends on avoiding catastrophic mistakes, even after decades of mostly beneficial decisions.

This perspective aligns with principles like Occam's razor and Solomonoff's theory of inductive inference, which favor simpler theories or models that sufficiently explain observed phenomena. The complexity of the model, and thus its capacity, is regulated to avoid overfitting and ensure better generalization.

### The Limits of Our Understanding

The inductive bias imposed by our limited working memory might be advantageous in the human niche, but it's essential to consider its potential shortcomings. Could there be aspects of reality that remain inaccessible to us due to our cognitive constraints?

Take, for instance, the phenomenon of consciousness. Understanding how self-awareness arises in a system may require accounting for the joint distribution of an astronomical number of variables. If this complexity is irreducible, our cognitive apparatus, bound by its inductive bias, may simply be inadequate for fully comprehending consciousness. 

Indeed, it's conceivable that vast swaths of reality might be fundamentally off-limits to human cognition, forever obscured by the constraints of our cognitive architecture. The complexity of these phenomena may defy simplification, making them impervious to our attempts at understanding through abstractions.

As we strive to push the boundaries of understanding, we should remain mindful of the limits imposed by our cognitive capacities and the constraints of our abstractions.

## The Unconscious Mind and LLMs

In my next blog post, I'll explore unconscious cognition, which makes up most of our mental activity (system 1 vs system 2 thinking), and the recent progress in machine learning, partciularly transformers and the LLM revolution.

