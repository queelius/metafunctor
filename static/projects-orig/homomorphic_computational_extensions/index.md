---
title: "Homomorphic computational extensions"
author: admin
summary: "Class of homomorphic monads that *extend* algebraic data types in some well-defined way for faciliating faster or more accurate calculations, such as the logarithm trick."
date: "2021-11-02"
external_url: "https://github.com/queelius/homomorphic_computational_extensions"
image:
  caption: Homomorphic mapping
  focal_point: Smart
  preview_only: yes
links:
- icon: github
  icon_pack: fab
  name: Code
  url: "https://github.com/queelius/homomorphic_computational_extensions"
categories:
- abstract data type
- monad
tags:
- homomorphism
- computation
- computer science
- bifunctor
---

## Introduction
We consider homomorphisms which are based on computational concerns which are used to transform inefficient or lossy computations over some original domain $T$ into a conceptually equivalent group $T^*$ over a restricted set of operations.

If the original problem can be solved using these restricted operations, then we may transform $T$ into $T^*$ and efficiently perform the computations. Sometimes, the entire solution cannot be transformed back to $T$, but the restricted set of functions or operations may still be sufficient, e.g., evaluating $a + c < b + c$ even though $a+c$ or $b+c$ may not be in the domain of $T$.

## Motivation
Suppose you have a value type $T$ (e.g., $\color{tan}\texttt{double}$ or
something more fancy), but when you apply some operations to it, undesirable
behavior is produced, e.g., $\color{tan}\texttt{double}$ overflows due to
multiplications or loses too much precision due to round-off error.

In the case of a numeric type like $\color{tan}\texttt{double}$, one option is
to use a big number library.
However, this approach has some disadvantages:

1. You may not want to have a dependency on some external library
2. The big number implementation may be too inefficient.

Assume the computational basis of $T$ is $F(T) = \\{*,/,+,-,<,==\\}$, which can be
used to relatively efficiently implement other operations like $\sin$.

Suppose we map $T$ to a modified type $\hat{T}$ such that the computational
basis $F(\hat{T})$ is a proper subset of $F(T)$, say $F(\hat{T}) = \\{*,/,<,==\\}$.
In exchange for this restriction, the undesired behavior may be avoidable, say
multiplications and divisions almost never overflow or underflow.
If we do not require the operations in basis $F(T)$, this is a good exchange.

Alternatively, if the computational basis $F(T)$ is needed, it may or may not be
possible to map $\hat{T}$ back to $T$ without any loss of information.

## Example
Suppose we try to compute $a!/b!$ (ratio of factorials). The end result may
be a value in the domain of $T$, but intermediate values (e.g., $a!$)
may only be in the domain of $\hat{T}$.
In this case, we can map the final result back to $T$ without any loss.

The logarithm of the data is a natural candidate for this is mapping $\color{tan}\texttt{double}$ to $\color{tan}\widehat{\texttt{double}}$
with the restricted basis

$$
\\{*,/,<,==,=\\}
$$

such that any of these operations almost never fail and can be performed
very efficiently, both in terms of time and space complexity.

### Code

```cpp
template <typename T = double>
struct lg
{
    T k;

    lg(lg const &) = default;

    // default constructs the multiplicative identity
    lg() : k(T(0)) {}

    lg(T x) : k(log(x)) { assert(0 < x); };

    // operator to convert (back) to type T.
    operator T() const { return exp(k); }
};

template <typename T>
auto operator*(lg<T> x, lg<T> y) { return lg<T>{x.k + y.k}; }

template <typename T>
auto operator/(lg<T> x, lg<T> y) { return lg<T>{x.k - y.k}; }

template <typename T>
auto operator<(lg<T> x, lg<T> y) { return x.k < y.k; }

template <typename T>
auto operator==(lg<T> x, lg<T> y) { return x.k == y.k; }
```
