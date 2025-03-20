---
title: 'Some Counterexamples'
date: 2025-03-19
permalink: /posts/2025/03/counterexamples
tags:
  - analysis
  - algebra
  - random
toc: true
---
{% include toc %}

Keeping track of some good counterexamples when needed.
I've roughly filtered them by topic for my own convenience.
Let me know if you spot any typos.


# Real Analysis
## Lebesgue null set that is also comeager
Let $Q_{k} = \bigcup_{q_n\in \mathbb{Q}}(q- 2^{-(n+k+1)}, q+2^{-(n+k+1)})$.
Then,
$$m(Q_k) \leq \sum_{n=1}^\infty 2^{-n-k} = 2^{-k}.$$
Moreover, since $\mathbb{Q}\subseteq Q_k$, $Q_k$ is dense.
It is also open since it is a union of open sets.
Let $Q = \bigcap_{k\in\mathbb{N}}Q_k$, so that $m(Q) = 0$ and $Q$ is dense, by the Baire Category Theorem.
Moreover,
$$Q^\complement = \left( \bigcap_{k\in\mathbb{N}}Q_k \right)^\complement =\bigcup_{k\in\mathbb{N}}Q_k^\complement.$$
Since $Q_k$ is open, $Q_k^\complement$ is closed.
Also let $x\in Q_k^\complement$.
Since $Q_k$ is dense, there exists $q$ such that $|x-q| < \varepsilon$, so $Q_k^\complement$ has empty interior.
Thus, $Q^\complement$ is a meagre subset of $\mathbb{R}$ whose complement has measure 0.

> This is not really surprising once you realize that topological properties usually do not correspond well with measure-theoretic properties.

## $\ell_0$, eventually zero sequences
This is a weird space.
Firstly, $\ell_0\subseteq\bigcap_{p\in (0,\infty]}\ell^p$: it is $\ell^p$ for every single $p$.
Also, every element of $\ell^p$ is a limit of a sequence in $\ell_0$ (for $p<\infty$).
Clearly, $\ell_0$ is not Banach in any of the usual norms.

Another weird property: $\ell_0$ has empty interior when viewed as a subset of $\ell^p$.

Also, when viewed as a subspace of $\ell^2$, there is _no_ orthogonal projection onto $\ell_0$. To see this note that $\inf_{y\in\ell_0}\|x-y\|_2 = 0$ for all $x\in\ell^2$, but if $x\notin\ell_0$ then this infimum is not achieved.
Alternatively note that if $x\in(\ell_0)^\perp$, then $x$ must be perpendicular to each of the canonical basis vectors, and thus $x = 0$.
Yet another perspective on this is that $\mathrm{cl}(\ell_0) = \ell^2$, so $((\ell_0)^\perp)^\perp = \ell^2$.
>This is an example of why closedness is necessary for orthogonal projections.

Even more bizarre, there is no norm on $\ell_0$ that makes it a complete vector space.
This can be seen [below](#a-vector-space-that-cannot-be-banach).

## There is no slowest rate of decay of an absolutely convergent series
Suppose that there exists a sequence of positive numbers $\\{a_n\\}\_{n\in\mathbb{N}}$ such that $\sum_{n\in\mathbb{N}}a_n|c_n| < \infty$ if and only if $\{c_n\}\_{n\in\mathbb{N}}$ is bounded.
Note that this implies that $A := \sum\_{n\in\mathbb{N}}a\_n <\infty$.
Then, the map 
$$T:\{c_n\}_{n\in\mathbb{N}}\mapsto\{a_nc_n\}_{n\in\mathbb{N}}$$
is an invertible linear map from $\ell^\infty$ to $\ell^1$.
Also,
$$ \|Tx\|_1 = \sum\_{n=1}^\infty a_n |x_n| \leq A\|x\|_\infty$$
so $T$ is bounded.
Therefore, $T^{-1}$ is bounded, since we are working with Banach spaces.
Note that by the previous part $\ell_0$ is dense in $\ell^1$, and $T(\ell_0)= \ell_0$.
However, $\ell_0$ is not dense in $\ell^\infty$, so such an isomorphism cannot exist.

More concretely, note that $\|\chi\_{\{n\}}\|_1 = 1$, but
$$ \|T^{-1}(\chi_{\{n\}})\|_\infty = \|a_n^{-1}\chi_{\{n\}}\|_\infty = a_n^{-1}.$$
Letting $n\to\infty$ we see that $T^{-1}$ is unbounded.
This is a contradiction, so no such sequence $\{a_n\}_{n\in\mathbb{N}}$ can exist.

## A vector space that cannot be Banach
Let $X$ be a a vector space of countably infinite dimension.
That is, there exists $\{e_n\}_{n\in\mathbb{N}}$ such that for all $x\in X$ there exist unique $e_{n_1},\dots, e_{n_k}$ and $c_1,\dots,c_k$ so that $x = \sum_{j=1}^k c_j e_{n_j}$.
Suppose there exists a norm on $X$ such that $X$ is complete.
Then, consider the subspaces 
$$A_n := \mathrm{span}\{e_1,\dots,e_n\}.$$
Note that $A_n$ is closed as it is finite dimensional.
It also has empty interior, since $(A_n + \varepsilon e_{n+1}) \cap A_n = \varnothing$ for all $\varepsilon > 0$.
Then, $X = \bigcup_{n\in\mathbb{N}}A_n$, but this contradicts the Baire Category Theorem.

## Uniform Limit of Differentiable Function is not Differentiable
Define
$$f_n(x)=\begin{cases}
-1, & x \leq -\frac{1}{n} \\
nx, & x \in \left[-\frac{1}{n},\frac{1}{n}\right] \\
1, & x \geq \frac{1}{n}
\end{cases}$$
Then, we define $F_n(x) = \int_{0}^x f_n(x)\,dx$.
Then,
$$ F_n(x) = \begin{cases}
    \frac{1}{2n}-x, & x\leq -\frac{1}{n} \\
    \frac{n}{2}x^2, & x \in \left[-\frac{1}{n},\frac{1}{n}\right] \\
    x - \frac{1}{2n}, & x\geq \frac{1}{n}.
\end{cases}$$
Note that $F_n\in C^1$, and $|F_n(x) - |x| | \leq \frac{1}{n}$ for all $x$.
Thus, $F_n\to |x|$ uniformly.

> There are probably simpler examples.

## $L^1\cap L^2_{\text{loc}} \not\subseteq L^2$
Consider
$$f(x) = \sum_{n=1}^{\infty} n\chi_{[n,n+\frac{1}{n^3}]}.$$
> This is basically saying that being $L^1$ on the whole space and $L^2$ on every bounded set is not sufficient to be $L^2$

# Linear Algebra
## Diagonalizability and invertibility have no relation
Consider $\begin{bmatrix}1 & 0 \\ 0 & 0 \end{bmatrix}$ and $\begin{bmatrix}1 & 1 \\ 0 & 1 \end{bmatrix}$.

## Matrix with no invariant subspace
The map from $\mathbb{R}^2\to\mathbb{R}^2$ defined by $\begin{bmatrix}
\frac{\sqrt{2}}{2} & -\frac{\sqrt{2}}{2} \\ \frac{\sqrt{2}}{2} & \frac{\sqrt{2}}{2} \end{bmatrix}$ (rotation by $90^\circ$) has no nontrivial invariant subspace.

> Algebraic completeness of the base field is important here.

# Group Theory
## Nonabelian group where every subgroup is normal
Of course, an example of this is $Q_8$, the quaternion group.
The nontrivial subgroups are $\{\pm 1\}, \{\pm 1, \pm i\}, \{\pm 1, \pm j\}, \{\pm 1, \pm k\}$.
> Almost as if this group was designed to be a counterexample.



# Ring Theory
## UFD that is not a PID
Consider $\mathbb{Z}[x,y]$ and the ideal $\langle x,y\rangle$.
If this ideal was generated by a single element, then it would have to divide $x$, but then it must be $x$.
However, $\langle x\rangle \lneq \langle x,y\rangle$.

## Integral Domain that is not a UFD
This is $\mathbb{Z}[\sqrt{5}i]$, since $6 = (1+\sqrt{5}i)(1-\sqrt{5}i) = 2\cdot 3$.

<!-- # Optimization -->
<!-- ## Lipschitz condition is needed for distributed gradient descent
Suppose each agent has cost functions $f_1(x) = \frac{1}{2}(x-n)^2$ and $f_2(x) = \frac{1}{2}(x+n)^2$.
Let the initial point be $x_1(0) = x_2(0)= 0$.
Then, 
$$\begin{align*}
    x_1(1) &= \frac{1}{2}x_1(0) + \frac{1}{2}x_2(0) - \eta_1(x_1(0)-n) =\eta_1 n\\
    x_2(1) &= \frac{1}{2}x_1(0) + \frac{1}{2}x_2(0) - \eta_1(x_2(0)+n) = -\eta_1 n
\end{align*}$$
By induction, we can see that $\frac{1}{2}x_1(k) + \frac{1}{2}x_2(k) = 0$ for all $k$.
Also, $x_2(1) = \eta_1 n - \eta_2(\eta_1 n - n)$ -->