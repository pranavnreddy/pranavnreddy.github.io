---
title: 'Some Notes on Hilbert Spaces'
date: 2025-03-17
permalink: /posts/2025/03/notes-on-hilbert-spaces
tags:
  - analysis
  - functional analysis
  - problems
toc: true
---

A few problems I thought about while working.


# $\ell^p(A)$ is always complete
If $A$ is empty, then let's agree to ignore this irrelevant case.

Otherwise, suppose $\{f_n\}\_{n\in\mathbb{N}}$ is a Cauchy sequence.
We would like to define $f = \lim_{n\to\infty}f_n$, where the limit is pointwise.
The nice thing about $\ell^p$ is that every point has mass (formally, every $\alpha\in A$ is an atom of the counting measure), so this idea actually works.

We first show that $\lim_{n\to\infty}f_n(\alpha)$ exists for each $\alpha\in A$.
Note that 
$$ |f_n(\alpha) - f_m(\alpha)|^p \leq \sum_{\beta\in A}|f_n(\beta) - f_m(\beta)|^p \to 0, \text{as }n,m\to\infty. $$
Thus, $\{f_n\}_{n\in\mathbb{N}}$ is pointwise Cauchy, and therefore $f=\lim_{n\to\infty}f_n$ is well-defined.

It's pretty clear that $f\in\ell^p(A)$, since 
$$\|f\|_p \leq \|f_n\|_p + \|f_n-f\|_p < \infty.$$

The proof for when $p=\infty$ is largely the same.

> This is not interesting since $L^p$ spaces are always complete, but it is an example of how counting measure lets us write pointwise estimates in terms of the $\ell^p$ norm.

# Every Orthonormal Basis Has the Same Size
Interestingly, this proof actually requires two different proofs in the finite and infinite dimensional cases.

## Finite Dimensions
This is basically the star result of finite-dimensional linear algebra, in particular the [Steinitz Exchange Lemma](https://en.wikipedia.org/wiki/Steinitz_exchange_lemma).

## Infinite Dimensions
We will assume we are working in an infinite-dimensional Hilbert space $H$.
Let $\\{v_\alpha\\}\_{\alpha\in A}$ and $\\{w_\beta\\}\_{\beta\in B}$ are two bases of the space.
Now, define 
$$E_\beta = \{ \alpha\in A : \langle w_\beta, v_\alpha\rangle \neq 0 \}.$$
Since $\|w_\beta\|^2 = \sum_{\alpha\in A} |\langle w_\beta, v_\alpha\rangle |$, we know that $E_\beta$ is countable.
Moreover, $A = \bigcup_{\beta\in B}E_\beta$.
If not, then there would exist $\alpha$ such that $\langle w_\beta, v_\alpha\rangle = 0$ for all $\beta$, implying that $v_\alpha = 0$ by [Parseval's Theorem](https://en.wikipedia.org/wiki/Parseval%27s_identity).
This clearly cannot happen.

Thus,
$$ |A| = \left| \bigcup_{\beta\in B} E_{\beta}\right| \leq |\mathbb{N}\times B | = |B|.$$
We are using the fact if $B$ is infinite then $|\mathbb{N} \times B| = |B|$ and the fact that each $E_\beta$ is at most countable.
However, nothing here was special about the role of $A$ and $B$, so reversing the argument shows that $|A| \leq |B|$ and $|B|\leq |A|$.
Applying the [Cantor-Schröder-Bernstein theorem](https://en.wikipedia.org/wiki/Schr%C3%B6der%E2%80%93Bernstein_theorem) completes the result.

## Corollary: $\ell^2(A) \cong \ell^2(B)$ if and only if $|A| = |B|$
This follows from noting that $\\{\chi_{\\{\alpha\\}}\\}\_{\alpha\in A}$ and $\\{\chi_{\\{\beta\\}}\\}\_{\beta\in B}$ form orthonormal bases of their respective spaces.

# Von Neumann's Mean Ergodic Theorem
