---
title: 'Notes on Randomized Linear Algebra'
date: 2026-09-20
permalink: /posts/2026/09/randomized-linear-algebra-notes
tags:
  - linear algebra
  - notes
  - probability
---

I've been learning a lot about randomized numerical linear algebra, and I've contributed some (very minor) ideas that I don't think are in the literature (yet).

{% include toc %}

# Parallelization Increases Convergence Rate (somewhat)
To my knowledge, it is an open problem to design an algorithm for solving the linera system $Ax = b$ that benefits from parallelization _in a way that is not a speedup of an underlying primitive operation._ To understand what this means, consider a standard iterative method, whose iterations might be written as something like 
$$ x_{k+1} = F_k x_{k} + b_k.$$
That is, the method applies an affine transformation to $x$ at each step.
This benefits from parallelization, since the primitive operation of matrix-vector arithmetic is amenable to parallelization.
However, the algorithm itself is still sequential: do the multiplication first (using whatever implementation you like), then do the addition, and then write to memory.

Let's try analyzing a very simple parallel algorithm.
Suppose we have $N$ processors, each having their own local iterate, denoted $x^{(i)}$ for the $i$-th processors iterate, and let $\bar{x}$ be their arithmetic mean.
For simplicity, let's also assume that each processor runs the exact same (possibly stochastic) algorithm for solving $Ax=b$.
Then, a simple application of the bias-variance decomposition yields

<p>
    \begin{align*}
        \mathbb{E}\left[\|\bar{x}-x^\star\|^2\right] &= \mathbb{E}\left[\|\bar{x}-\mathbb{E}\left[\bar{x}\right]\|^2\right] + \|\mathbb{E}\left[\bar{x}\right]-x^\star\|^2 \\
        &= \frac{1}{N}\mathbb{E}\left[\|x-\mathbb{E}\left[x\right]\|^2\right] + \|\mathbb{E}\left[x\right]-x^\star\|^2 \\
        &= \frac{1}{N}\left(\mathbb{E}\left[\|x-x^\star\|^2\right] - \|\mathbb{E}\left[x\right]-x^\star\|^2\right) + \|\mathbb{E}\left[x\right]-x^\star\|^2 \\
        &= \frac{1}{N}\mathbb{E}\left[\|x-x^\star\|^2\right] + \frac{N-1}{N}{\|\mathbb{E}\left[x\right]-x^\star\|^2}.
    \end{align*}
</p>

It is known that the sketch-and-project methods converge at a faster rate than their MSE (see [Gower's thesis, Table 2.1](https://arxiv.org/pdf/1612.06013)), so in theory this gives a "free" gain of a squared factor.

## The Caveat
This method kind of sucks actually, when measured in matrix-vector operations instead of iteration complexity (one reason why per-iteration complexity is deceiving).
Say we use the Kaczmarz method on each processor, and for simplicity assume they do one Kaczmarz step before averaging.
Note that the randomized Kaczmarz method has a linear (exponential if you aren't a numerical analysis person) convergence rate of $O(\alpha^k)$, where $\alpha = 1 - \frac{\sigma_{\min}(A)^2}{\|A\|_F^2}$.
This "parallel" implementation requires roughly $N$ matrix-vector multiplies and adds, as well as an additional averaging step.
If we instead spent those matrix-vector multiplies on just doing more iterations, we could gain a convergence factor of $\alpha^N$, instead of the $\frac{1}{N}\alpha + \frac{N-1}{N}\alpha^2$, which actually scales poorly with $N$: we should just do more Kaczmarz steps rather than bother with averaging.
The case gets even worse when you drill down and consider the synchronization costs and so forth.

# A Brief Overview of Right Sketches
I have not seen a clean overview of an equivalent right-sketch framework in the style of [Gower's thesis](https://arxiv.org/pdf/1612.06013).
I believe an appropriate framework for understanding them is a **low-rank update**.
Suppose we want to solve $Ax = b$, and we have some initial candidate solution $x_0$.
Consider the problem
$$ \min_{u}\|A(x_0+u) - b\| = \min_{u}\|Au - (b-Ax_0)\|.$$
This is attempting to find the best step that minimizes the residual.
Obviously, reparametrizing shows that the problem as stated is equivalent to solving the least-squares problem $\min_{x}\|Ax-b\|$.
However, this may be hard, and we might want to take advantage of "warm-starting" our method with $x_0$.
If $A\in\mathbb{R}^{m\times n}$ is wide ($m < n$), the solutions to the system lie in an affine subspace of at most dimension $m$. How can we search for such a subspace effectively? Let's try a *random* subspace, and go from there.
That is, let's solve the problem 
$$ \min_{u}\|AR(x_0+u) - b\| = \min_{u}\|ARu - (b-Ax_0)\|,$$
where $R\in\mathbb{R}^{n\times p}$ is a *sketch* that reduces the size of the problem.
$R$ doesn't necessarily need to be random (you could choose it via some deterministic rule), but randomness makes the analysis easier (and more interesting).
The solution to the above problem is
$$u = (AR)^\dagger(b-Ax_0),$$
where $B^\dagger$ denotes the [Moore-Penrose inverse](https://en.wikipedia.org/wiki/Moore%E2%80%93Penrose_inverse) of $B$.
and so the update is 
$$x_+ = x_0 + Ru = x_0 + R(AR)^\dagger(b-Ax_0).$$
This is an affine dynamical system, so let's see how the error evolves to hopefully get a *linear* dynamical system:

<p>
\begin{align*}
        Ax_+ -b &= A(x_0 + Ru) - b \\
        &= A(x_0 + R(AR)^\dagger(b-Ax_0)) - b \\
        &= (Ax_0 - b) - AR(AR)^\dagger(Ax_0 - b) \\
        &= (I - AR(AR)^\dagger)(Ax_0 - b)
    \end{align*}
</p>

This is good so far, so let's now analyze the norm of the least-squares residual.
We'll use the fact that for any matrix $B$, $BB^\dagger$ is symmetric and that $I - BB^\dagger$ is the orthogonal projection onto the kernel of $B$.
Let's start by expanding the square:

<p>
    \begin{align*}
        \|Ax_+ -b\|^2 &= \|(I - AR(AR)^\dagger)(Ax_0 - b)\|^2 \\
        &= (Ax_0 - b)^\top(I - AR(AR)^\dagger)^\top(I - AR(AR)^\dagger)(Ax_0 - b) \\
        &= (Ax_0 - b)^\top(I - AR(AR)^\dagger)^2(Ax_0 - b) \\
        &= (Ax_0 - b)^\top(I - AR(AR)^\dagger)(Ax_0 - b).
    \end{align*}
</p>

Taking expectations, (assuming that $R$ is independent of $x_0$), we find

<p>
    \begin{align*}
        \mathbb{E}[\|Ax_+ -b\|^2] &= \mathbb{E}[\mathbb{E}[(Ax_0 - b)^\top(I - AR(AR)^\dagger)(Ax_0 - b) \mid x_0]] \\
        &= \mathbb{E}[(Ax_0 - b)^\top\mathbb{E}[I - AR(AR)^\dagger\mid x_0](Ax_0 - b) ] \\
        &= \mathbb{E}[(Ax_0 - b)^\top\mathbb{E}[I - AR(AR)^\dagger](Ax_0 - b) ] \\
        &\leq \lambda_{\max}(\mathbb{E}[I - AR(AR)^\dagger])\mathbb{E}[\|Ax_0 -b\|^2] \\
        &= (1 - \lambda_{\min}(\mathbb{E}[AR(AR)^\dagger]))\mathbb{E}[\|Ax_0 -b\|^2] \\
        &= (1 - \lambda_{\min}(\mathbb{E}[AR(R^\top A^\top AR)^\dagger R^\top A^\top]))\mathbb{E}[\|Ax_0 -b\|^2].
    \end{align*}
</p>

We used a standard result on pseudoinverses to get the equality in the last line.

## Examples
### Randomized Coordinate Descent ([Leventhal & Lewis, 2018](https://arxiv.org/pdf/0806.3015)) 
Choose $R = e_i$ (the standard basis vector) with probability $\frac{\|a_i\|^2}{\|A\|_F^2}$, where $a_i$ is the $i$-th column of $A$, to get the convergence rate of the paper: $1 - \lambda_{\min}(\mathbb{E}[AR(R^\top A^\top AR)^\dagger R^\top A^\top]) = 1 - \frac{\sigma_{\min}(A)^2}{\|A\|_F^2}$.
Indeed, in this framework it's pretty clear why randomized coordinate descent converges to the least-squares solution even for an inconsistent system: the algorithm searches for the best low-rank update to minimize the least-squares residual.

### Gaussian Coordinate Descent
Let $R \sim \mathcal{N}(0, I)$, so 
$$AR(R^\top A^\top AR)^\dagger R^\top A^\top = \frac{ARR^\top A^\top}{\|AR\|^2}.$$
Note that $AR\sim\mathcal{N}(0,AA^\top)$.
If $A$ has full row rank, then we can apply [Lemma 20 of Gower's thesis](https://arxiv.org/pdf/1612.06013) to see that 

$$\mathbb{E}\left[\frac{ARR^\top A^\top}{\|AR\|^2}\right] \succeq \frac{2}{\pi}\frac{AA^\top}{\|A\|^2_F}$$

> In fact, we don't really need the full row rank to write the matrix inequality above, but it makes the analysis nicer.

Thus, 
$$ 1 - \lambda_{\min}(\mathbb{E}[AR(R^\top A^\top AR)^\dagger R^\top A^\top]) \leq 1 - \frac{2}{\pi}\frac{\sigma_{\min}(A)^2}{\|A\|_F^2}.$$

## Remarks
This can be further generalized to norms defined by an arbitrary positive definite matrix $Q$, but that would be too long and this is already too much math.
Rather, I hope this provides a "dual" view on sketch-and-project methods, which frequently analyze tall matrices ($m > n$, more rows than columns) that arise in data science applications.
For an optimizer, however, wide matrices are much more common, since the full column rank assumption present in these works would make optimization trivial (there would only be 1 feasible point in the linear equality $Ax = b$).
Moreover, instead of a guarantee on iterate convergence, an analysis of right sketches seems to naturally favor guarantees on convergence of the function value.
