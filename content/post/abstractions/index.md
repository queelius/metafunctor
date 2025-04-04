---
title: Uses and limits of abstractions
author: admin
date: '2023-06-17'
featured: no
comments: yes
slug: uses-and-limits-of-abstractions
categories:
- philosophy
- machine-learning
image:
  caption: ''
  focal_point: ''
  preview_only: yes
projects: []
---

I'm been thinking about the power and limitations of abstractions in our
understanding of the world. This blog post is from a chat I had with a ChatGPT,
which can be found [here](https://chat.openai.com/share/f298898b-9787-48f4-8959-c8cd04eb98b4)
and [here](https://chat.openai.com/share/0d33ab33-0664-4b25-b1c2-22864b28db48).

> I'm not sure if this is a good blog post, but I'm posting it anyway. It's remarkable
> how quickly you can slap stuff like this together, and I'm not sure this is
> saying anything valuable, particularly since it only required a bit of prompting
> from me.

***

<img src="./featured.jpg" style="width: 200px; float: left; margin: 10px;">

## Uses and limits of abstractions

Reality, in all its richness, is far more complex than we can appreciate. Our attempts
to understand and navigate it necessitate the use of abstractions, compressions that
retain the salient details relevant to a specific context while discarding the rest.
These abstractions are indispensable to human cognition, enabling us to engage with
parts of reality despite
our limited cognitive capacity and incomplete information, but there are also
parts of reality that may be fundamentally off-limits to us.

### Limited working memories

Human cognitive abilities are bounded. For instance, our working memory can effectively
hold and process only a limited amount of information at once. Cognitive psychology often references the "magic number seven", suggesting that most adults can hold between five and nine items in their working memory.

Consider a situation where we're dealing with multiple variables $(x_1, x_2, x_3, x_4)$. Our brain might struggle to simultaneously process the joint distribution of these variables due to the limitation of our working memory. However, if we create an abstraction where $X$ represents $(x_1, x_2)$ and $Y$ represents $(x_3, x_4)$, we simplify the cognitive task to handling the joint distribution of just two variables $(X,Y)$, which is a more manageable task.
This constraint necessitates the use of abstractions in order to understand complex systems.

### Incomplete information

Beyond our cognitive limitations, we also lack complete information about any 
real-world system. We cannot have total information about the systems we're trying to
understand.

Again, we may use abstractions to deal with this limitation, abstractions that allow
us to think more clearly about some parts of the system (that can be observed
and usefully reduced) while ignoring other parts (that are not observable or not
usefully compressible). For instance, a key concept in statistical mechanics is entropy, which allows us to reason about the behavior of systems with a large number of particles
that behave according to some statistical regularties in the aggregate. We might be
able to observe certain features of a system, such as the size of a box and its temperature, but there's much we don't know, such as the microstates the system is in at a given moment.

However, knowing the temperature and size of the box, we can make useful predictions about the system, such as what
its temperature in one hour will be, or whether it will explode if we add a certain
amount of heat to it. We can ask certain questions about it, but we cannot ask
questions that require knowing the microstate of the system. And, ultimately,
everything about the system is determined by its microstate, and so there are many
questions we cannot answer.

Entropy allows us to reason about the system using available observations, while acknowledging the underlying complexity that we can't observe or don't yet understand. More generally, despite our limited understanding and inability to perceive the entirety of a complex system, we still aim to make meaningful assertions about it. This is where the role of abstractions, like entropy, becomes particularly significant, acting as a cognitive scaffold that allows us to grasp some aspects of the system's behavior.

The idea of entropy itself can be generalized. It is a compelling concept in
information theory, defined as
$$
H(X) = -\sum_{x \in X} p(x) \log p(x),
$$
where $X$ is a random variable and $p(x)$ is the probability of $X$ taking on the value $x$.
When dealing with microstates and supposing that each state is equally probable (which is a reasonable approximation in the case of a gas-filled box), entropy "simplifies"
to the logarithm of the number of different states the system can be in that are
compatible with what we can observe about the system.

### Emergent behavior

It's important to remember, however, that such reasoning can only get us so far. Some systems have a complexity that is fundamentally irreducible, characterized by emergent behavior that can only be discerned when considering the system as a whole. This is related to our limited
working memories, and our need for creating abstractions to work-around our limitations.
However, there are some systems that are so complex that they cannot be reduced to
simpler parts. The behavior of the system as a whole is not just the sum of the
behavior of its parts, but is something new and different. This is known as
emergent behavior, and it is a key feature of many complex systems.

Consider the earlier example, where we were dealing with multiple variables $(x_1, x_2, x_3, x_4)$ and reduced it to just two variables $(X,Y)$, where $X = (x_1, x_2)$ and $Y = (x_3, x_4)$. This abstraction is useful in many contexts, but it is not always appropriate. For instance, if $x_1$ and $x_4$ are correlated in some significant way, perhaps only in the distant future, 
then the reduction to $X$ and $Y$ may fail to capture this salient feature. In this
case, to understand the important parts of the system that we are interested in, we
need to consider the joint distribution of the full set of variables $(x_1, x_2, x_3, x_4)$.

A popular example is "water is wet". This is a true statement, but it is not true of any of the individual molecules that make up water. It is an emergent property of the system as
a whole, in which billions of these simple molecules interact in locally simple ways.
The wetness of water is not a property of the individual molecules, but an emergent
property of the system as a whole.

It may even the case that something like consiousness is an emergent property in an
even more complex way, such that it cannot be reduced to the behavior of individual
neurons or small groups of neruons, but only emerges as a property of the integrated
behavior of the entire brain. Indeed, we may be talking about a system
with $(x_1, x_2, \ldots, x_n)$, $n = \mathcal{O}(\text{\# neurons})$.

Let's consider again the concept of entropy. The representation of a system, such that it cannot
be significantly compressed without losing vital information, may be said to have
emerget properties. The earlier example of a box of gas is a good example that can be
*significantly* compressed by considering only its temperature and dimensions. Knowing
that information allows us to say a lot about the system.
Systems for which a useful compression is not possible may be said to have emergent
properties. When this is the case, we cannot compress the system in a way
that fits our cognitive limitations, or in a way that is not sensitive to a lack
of knowlege.

This is why consciousnes may feel so mysterious
and inexplicable, because we cannot understand it, we cannot reduce it to a simpler
system that we can, say, program on a computer using our cognition. (This is where
machine learning, deep learning, LLMs, and so on become particularly relevant
for solving problems that are too complex for us to solved analytically.)

### Abstractions as cognitive scaffolds

Abstractions are indispensable to human cognition, enabling us to engage with reality despite our limited cognitive capacity. They allow us to reason about complex systems, even when we lack complete information about them. They also allow us to communicate with others, sharing ideas and knowledge across fields and disciplines. In creating abstractions, we walk a delicate balance. We need to remember these key aspects:

1. **Imperfect representation**

    Abstractions are, by design, reductions of reality.
    While they help us manage complexity, most will fail to capture all the necessary information. As our needs evolve, we find ourselves tweaking the abstraction, adding layers of complexity that can make it harder to reason about and thereby diluting its initial utility. There is a constant balancing act in maintaining simplicity while preserving relevance.

    This is also known as "the map is not the territory" problem. The map is a useful representation of the territory, but it is not the territory itself.

2. **Abstractions are contextual**

    They are useful in certain contexts but not in others. We must be aware of the context in which we are using an abstraction and understand its limitations. We must also be aware of the context in which the abstraction was created and the assumptions that were made in its creation. This is particularly important when using abstractions from other fields.

3. **Pedagogical**

    While an abstraction might not always provide an accurate representation of the object in real-world settings, it serves a valuable educational purpose. It enables us to learn key features about the object and acts as a bootstrap technique for further understanding. Even when ready-made abstractions fall short in our context, we can use principles of reductionism, analogy, and more to grapple with the complexity. Yet, we must remember that many phenomena are cross-cutting or emergent and cannot be fully understood through this process.

4. **Communication**

    Abstractions are a key tool for communication. They allow us to share ideas and knowledge with others, even those outside our field. However, since an expert is often aware of the
    many nuances and limitations of an abstraction, they might not be the best person to explain it to a novice. It's important to frequently ask, "How could I explain this to a five-year-old?" This helps us stay grounded, facilitates cross-pollination of ideas, and allows for broader comprehension.

These pointes demonstrate a dance between simplicity and complexity that we engage in when creating and using abstractions. Like a map, an abstraction is not the territory but a simplified representation of it. As we journey through the landscape of knowledge, these representations guide us, helping us 'see the forest for the trees', even as we acknowledge their inherent limitations.

### Conclusion

Abstractions are indispensable to human cognition, enabling us to engage with reality despite our limited cognitive capacity and incomplete information. They allow us to reason about complex systems, even when we lack complete information about them. They also allow us to communicate with others, sharing ideas and knowledge across fields and disciplines.

However, we must be cognizant of the limitations of abstractions. They are, by design, reductions of reality. While they help us manage complexity, most will fail in some way. Much of
reality may, in fact, be computationally irreducible (to borrow a phrase from Wolfram).
