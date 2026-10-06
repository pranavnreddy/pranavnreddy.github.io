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
To my knowledge, it is an open problem to design an algorithm for solving the linear system $Ax = b$ that benefits from parallelization _in a way that is not a speedup of an underlying primitive operation._ To understand what this means, consider a standard iterative method, whose iterations might be written as something like 
<p>
\begin{equation*}
    x_{k+1} = F_k x_{k} + b_k.
\end{equation*}
</p> 
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

It is known that the bias of sketch-and-project methods converge at a faster rate than their MSE (see [Gower's thesis, Table 2.1](https://arxiv.org/pdf/1612.06013)), so in theory this gives a "free" gain of a squared factor.

## The Caveat
This method kind of sucks actually, when measured in matrix-vector operations instead of iteration complexity (one reason why per-iteration complexity is deceiving).
Say we use the Kaczmarz method on each processor, and for simplicity assume they do one Kaczmarz step before averaging.
Note that the randomized Kaczmarz method has a linear (exponential if you aren't a numerical analysis person) convergence rate of $O(\alpha^k)$, where $\alpha = 1 - \frac{\sigma_{\min}(A)^2}{\\|A\\|_F^2}$.
This "parallel" implementation requires roughly $N$ matrix-vector multiplies and adds, as well as an additional averaging step.
If we instead spent those matrix-vector multiplies on just doing more iterations, we could gain a convergence factor of $\alpha^N$, instead of the $\frac{1}{N}\alpha + \frac{N-1}{N}\alpha^2$, which actually scales poorly with $N$: we should just do more Kaczmarz steps rather than bother with averaging.
The case gets even worse when you drill down and consider the synchronization costs and so forth.
Feel free to try running your own numerical experiments and let me know if you agree or disagree.

# A Brief Overview of Right Sketches
I have not seen a clean overview of an equivalent right-sketch framework in the style of [Gower's thesis](https://arxiv.org/pdf/1612.06013).
I believe an appropriate framework for understanding them is a **low-rank update**.

Suppose we want to solve $Ax = b$, and we have some initial candidate solution $x_0$.
Consider the problem

<p> 
\begin{equation*}
    \min_{u}\|A(x_0+u) - b\|^2 = \min_{u}\|Au - (b-Ax_0)\|^2.
\end{equation*}
</p>

This is attempting to find the best step that minimizes the residual.
Obviously, reparametrizing shows that the problem as stated is equivalent to solving the least-squares problem $\min_{x}\\|Ax-b\\|$.
However, this may be hard, and we might want to take advantage of "warm-starting" our method with $x_0$.
If $A\in\mathbb{R}^{m\times n}$ is wide ($m < n$), the solutions to the system lie in an affine subspace of at most dimension $m$. How can we search for such a subspace effectively? Let's try a *random* subspace, and go from there.
That is, let's solve the problem 

<p> 
\begin{equation*}
    \min_{u}\|AR(x_0+u) - b\|^2 = \min_{u}\|ARu - (b-Ax_0)\|^2,
\end{equation*}
</p>

where $R\in\mathbb{R}^{n\times p}$ is a *sketch* that reduces the size of the problem.
$R$ doesn't necessarily need to be random (you could choose it via some deterministic rule), but randomness makes the analysis easier (and more interesting).
The solution to the above problem is

<p> 
\begin{equation*}
    u = (AR)^\dagger(b-Ax_0),
\end{equation*}
</p>

where $B^\dagger$ denotes the [Moore-Penrose inverse](https://en.wikipedia.org/wiki/Moore%E2%80%93Penrose_inverse) of $B$.
and so the update is 

<p> 
\begin{equation*}
    x_+ = x_0 + Ru = x_0 + R(AR)^\dagger(b-Ax_0).
\end{equation*}
</p>

This is an affine dynamical system, so let's see how the error evolves to hopefully get a *linear* dynamical system:

<p>
    \begin{align*}
        Ax_+ -b &= A(x_0 + Ru) - b \\
        &= A(x_0 + R(AR)^\dagger(b-Ax_0)) - b \\
        &= (Ax_0 - b) - AR(AR)^\dagger(Ax_0 - b) \\
        &= (I - AR(AR)^\dagger)(Ax_0 - b).
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
Choose $R = e_i$ (the standard basis vector) with probability $\frac{\\|a\_i\\|^2}{\\|A\\|\_F^2}$, where $a_i$ is the $i$-th column of $A$, to get the convergence rate of the paper:

<p>
\begin{equation*}
    1 - \lambda_{\min}(\mathbb{E}[AR(R^\top A^\top AR)^\dagger R^\top A^\top]) = 1 - \frac{\sigma_{\min}(A)^2}{\|A\|_F^2}.
\end{equation*}
</p>
Indeed, in this framework it's pretty clear why randomized coordinate descent converges to the least-squares solution even for an inconsistent system: the algorithm searches for the best low-rank update to minimize the least-squares residual.

### Gaussian Coordinate Descent
Let $R \sim \mathcal{N}(0, I)$, so 

<p> 
\begin{equation*}
    AR(R^\top A^\top AR)^\dagger R^\top A^\top = \frac{ARR^\top A^\top}{\|AR\|^2}.
\end{equation*}
</p> 

Note that $AR\sim\mathcal{N}(0,AA^\top)$.
If $A$ has full row rank, then we can apply [Lemma 20 of Gower's thesis](https://arxiv.org/pdf/1612.06013) to see that 

<p> 
\begin{equation*}
    \mathbb{E}\left[\frac{ARR^\top A^\top}{\|AR\|^2}\right] \succeq \frac{2}{\pi}\frac{AA^\top}{\|A\|^2_F}.
\end{equation*}
</p> 

> In fact, we don't really need the full row rank assumption to write the matrix inequality above, but it makes the analysis nicer.

Thus,

<p> 
\begin{equation*}
    1 - \lambda_{\min}(\mathbb{E}[AR(R^\top A^\top AR)^\dagger R^\top A^\top]) \leq 1 - \frac{2}{\pi}\frac{\sigma_{\min}(A)^2}{\|A\|_F^2}.
\end{equation*}
</p> 

## Remarks
This can be further generalized to norms defined by an arbitrary positive definite matrix $Q$, but that would be too long and this is already too much math.
Rather, I hope this provides a "dual" view on sketch-and-project methods, which frequently analyze tall matrices ($m > n$, more rows than columns) that arise in data science applications.
For an optimizer, however, wide matrices are much more common, since the full column rank assumption present in these works would make optimization trivial (there would only be 1 feasible point in the linear equality $Ax = b$).
Moreover, instead of a guarantee on iterate convergence, an analysis of right sketches seems to naturally favor guarantees on convergence of the function value.


# Randomized (Block) Kaczmarz Converges to the Projection of the Initial Iterate
## Simple Kaczmarz: One row at a time
This is a somewhat interesting result.
Suppose we want to solve the system $Ax = b$, where $A \in \mathbb{R}^{m\times n}$ and $m < n$.
Very often, we may want to find a solution which minimizes the displacement from a starting point.
In mathematical terms, this is the projection of the solution onto a hyperplane, which we'll denote as $\Pi(x)$.z
That is, we want to solve

<p> 
\begin{align*}
    \min_{x \in \mathbb{R}^n} &\quad \|x - x_0\|^2_2 \\
    \text{subject to} &\quad Ax= b.
\end{align*}
</p>

Let's first note that the projection onto a hyperplane has a closed-form solution.
The problem above is equivalent to solving

<p> 
\begin{align*}
    \min_{z \in \mathbb{R}^n} &\quad \|z\|^2_2 \\
    \text{subject to} &\quad Az= b-Ax_0.
\end{align*}
</p>

In essence, we defined $z=x-x_0$ and rewrote the problem in terms of $z$.
Since $x_0$ is known, we can apply the known fact that the pseudoinverse finds the least-norm solution of a linear system to see that 

<p> 
\begin{align*}
    x^* = x_0 + z^* = x_0 + A^\dagger(b-Ax_0) = (I - A^\dagger A) x_0 + A^\dagger b.
\end{align*}
</p>

An interesting fact is that the Kaczmarz method (for any row selection procedure), converges to the solution of the aforementioned projection problem.
Recall that the [Kaczmarz method](https://en.wikipedia.org/wiki/Kaczmarz_method) does iterative projections onto each of the $m$ hyperplanes defined by $\langle a_i, x\rangle = b_i$, where $a_i$ is the $i$-th row of $A$ and $b_i$ is the $i$-th coordinate of $A$.
You can get some intuition for this by noting that 

<p> 
\begin{align*}
    Ax = b \iff \langle a_1, x\rangle &= b_1 \\
    \langle a_2, x\rangle &= b_2 \\
    &\vdots \\
    \langle a_m, x\rangle &= b_m.
\end{align*}
</p>

A very simple idea to try to approximate a solution is to just iteratively project a point onto one of the $m$ hyperplanes at each iteration.
In closed form, this is 

<p> 
\begin{align*}
    x_{k+1} = x_k + \frac{b_{i_k} - \langle a_{i_k}, x_k\rangle}{\|a_{i_k}\|^2} a_{i_k}.
\end{align*}
</p>

One can check that $\langle x_{k+1}, a_{i_k} \rangle = b_{i_k}$.


Let's see what happens to the projection of the iterate as we run the Kaczmarz method on this problem.
If $a_{i_k}$ is the row chosen at iteration $k$, then

<p>
\begin{align*}
    \Pi(x_{k+1}) &= (I - A^\dagger A) x_{k+1} + A^\dagger b \\
    &= A^\dagger b + (I - A^\dagger A) \left(x_k + \frac{b_{i_k} - \langle a_{i_k}, x_k\rangle}{\|a_{i_k}\|^2}a_{i_k}\right) \\
    &= A^\dagger b + (I - A^\dagger A) x_k + (I - A^\dagger A)\left(\frac{b_{i_k} - \langle a_{i_k}, x_k\rangle}{\|a_{i_k}\|^2}a_{i_k}\right) \\
    &= \Pi(x_k) + \alpha(I - A^\dagger A) A^\top e_i \\
    &= \Pi(x_k).
\end{align*}
</p>

We combined scalar constants into $\alpha_i$ to separate the essence of the proof.
From a well-known result about pseudo inverses, $A^\dag A A^\top = A^\top$, which is why the second term vanishes.

> In fact, this shows that in general, any method where the updates are in the span of the rows leaves the projection invariant. This also shows that running gradient descent on the objective $\|Ax-b\|^2$ preserves the projection.

Since the projection is invariant under row-action methods, we just need to show that the method actually converges to this invariant.
Let's expand the error dynamics and see what comes out:

<p>
\begin{align*}
    \|x_{k+1} - \Pi(x_{k+1})\|^2 &= \|x_{k} - \Pi(x_k)\|^2 -2\left\langle x_{k} - \Pi(x_k), \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}a_{i_k} \right\rangle+ \left\|\frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}a_{i_k}\right\|^2.
\end{align*}
</p>

Let's focus on the inner product term:
<p>
    \begin{align*}
        \left\langle x_{k} - \Pi(x_k), \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}a_{i_k} \right\rangle &= \left\langle A^\dagger (Ax_{k} - b ), \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}a_{i_k} \right\rangle \\
        &= \left\langle A^\dagger (Ax_{k} - b ), \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}A^\top e_i \right\rangle \\
        &= \left\langle A A^\dagger (Ax_{k} - b ), \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}e_i \right\rangle\\
        &= \left\langle Ax_{k} - b, \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}e_i \right\rangle \\
        &= \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2} \cdot \left\langle Ax_{k} - b, e_i \right\rangle \\
        &= \frac{(b_{i_k} - \langle a_{i_k} , x_k\rangle)^2}{\|a_{i_k}\|^2} \\
        &= \left\|\frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}a_{i_k}\right\|^2  \\
        &= \|x_{k}-x_{k+1}\|^2.
    \end{align*}
</p>

Thus,

<p>
\begin{align*}
    \|x_{k+1} - \Pi(x_{k+1})\|^2 &= \|x_{k} - \Pi(x_k)\|^2 -2\left\langle x_{k} - \Pi(x_k), \frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}a_{i_k} \right\rangle+ \left\|\frac{b_{i_k} - \langle a_{i_k} , x_k\rangle }{\|a_{i_k}\|^2}a_{i_k}\right\|^2 \\
    &= \|x_{k} - \Pi(x_k)\|^2 -2\|x_{k}-x_{k+1}\|^2 + \|x_{k}-x_{k+1}\|^2 \\
    &= \|x_{k} - \Pi(x_k)\|^2 - \|x_{k}-x_{k+1}\|^2.
\end{align*}
</p>


Since $\Pi(x_{k+1}) = \Pi(x_k) = \dots = \Pi(x_0)$, we find that the distance from the projection is monotone decreasing.

Now, we just bound $\|x_k - x_{k+1}\|^2$ in terms of $\|x_{k} - \Pi(x_k)\|^2$.

<p>
\begin{align*}
    \|x_{k}-x_{k+1}\|^2 = \frac{(b_{i_k} - \langle a_{i_k} , x_k\rangle)^2}{\|a_{i_k}\|^2} &= \frac{(\langle a_{i_k}, \Pi(x_k)\rangle - \langle a_{i_k} , x_k\rangle)^2}{\|a_{i_k}\|^2} \\
        &= \frac{(\langle a_{i_k}, \Pi(x_k) - x_k\rangle)^2}{\|a_{i_k}\|^2} \\
        &= (\cos\theta_{k})^2 \|\Pi(x_k) - x_k\|^2,
\end{align*}
</p>

where $\cos\theta_{k}$ is the angle between $\Pi(x_k) - x_k$ and $a_{i_k}$.
Thus,

<p>
\begin{align*}
    \|x_{k+1} - \Pi(x_{k+1})\|^2 &= \|x_{k} - \Pi(x_k)\|^2 - \|x_{k}-x_{k+1}\|^2 \\
    &= \|x_{k} - \Pi(x_k)\|^2 - (\cos\theta_{k})^2 \|\Pi(x_k) - x_k\|^2 \\
    &= (1-(\cos\theta_{k})^2 )\|\Pi(x_k) - x_k\|^2 \\
    &= (\sin\theta_{k})^2\|\Pi(x_k) - x_k\|^2 \\
    &= (\cos(\varphi_{k}))^2\|\Pi(x_k) - x_k\|^2,
\end{align*}
</p>

where $\varphi_k = \frac{\pi}{2} - \theta_k$ is the complementary angle.
Recall from the definition of the Kaczmarz method that $x_k$ lives on the hyperplane $\{x : \langle a_{i_{k-1}}, x \rangle  = b_{i_{k-1}}\}$, and so does $\Pi(x_k)$ by virtue of solving $Ax = b$ (assuming we've done at least one projection already).
Thus, $\langle a_{i_{k-1}}, \Pi(x_k) - x_k\rangle = 0$, so $\theta_{k}$ is the angle between the hyperplane with $a_{i_{k-1}}$ as its normal vector and the vector $a_{i_k}$.
By some elementary geometry (or manipulating inner products), we can see that $\cos\varphi_{k}$ is then the angle between the vectors $a_{i_k}$ and $a_{i_{k-1}}$.
Moreover, $\cos\varphi_k = 1$ if and only if $a_{i_k}$ and $a_{i_{k-1}}$ are parallel.
If all rows are parallel, then one we are done in one projection.
Otherwise, we can upper bound $\cos\varphi_k$ by the smallest angle between the rows, and thus we find geometric convergence to the projection.
That is,

<p>
\begin{align*}
    \|x_{k+1} - \Pi(x_{k+1})\|^2 &\leq (\cos(\varphi^*))^2\|\Pi(x_k) - x_k\|^2,
\end{align*}
</p>

where $\varphi^* < 1$ is the smallest angle between two rows (assuming there are no parallel rows).

## The Block Case
Since Kaczmarz iterates by projecting onto one row at a time, maybe we can get faster convergence if we try projecting onto multiple rows at once.
That is, at each step, project onto $\{x : A_{I}x = b_I\}$, where $I\subset[1:m]$ is some subset of the row indices of $A$.
It's a nice result that the block case preserves this convergence property.

Since at each step, we project $x_k$ onto $\{x : A_{I}x = b_I\}$ for some set of row indices $I$, we can solve this projection via _a separate run of the Kaczmarz algorithm_.
That is,

<p>
\begin{align*}
    x_{k+1} = \Pi_I(x_k) = \lim_{j\to\infty} z_j,
\end{align*}
</p>

where $z_j$ are a sequence of iterates with $z_0 = x_k$ and $z_{j+1} = z_j + \frac{b_{i_j} - \langle a_{i_j}, z_j\rangle}{\|a_{i_j}\|^2}$, restricting the choice of row indices to have $i_j \in I$.
By the results of the previous section, this leaves the projection of $z_j$ onto the solution set $\{x : Ax = b\}$ invariant.
Since the projection onto a hyperplane is a continuous map, the limit also has this property.
More formally,

<p>
\begin{align*}
    \Pi(x_{k+1}) = \Pi(\Pi_I(x_k)) = \Pi\left(\lim_{j\to\infty} z_j\right) = \lim_{j\to\infty}\Pi( z_j) = \lim_{j\to\infty} \Pi(x_k) = \Pi(x_k).
\end{align*}
</p>

> There are other ways to show this, but I think this is an interesting method and motivates some further questions about inexactness in block Kaczmarz or cache-friendly sampling strategies (e.g. using Kaczmarz to solve block Kaczmarz)

Now, we can explicity write the update as

<p>
\begin{align*}
    x_{k+1} = x_k + A_I^\dagger (b_I - A_I x_k).
\end{align*}
</p>

Thus, the error dynamics are
<p>
\begin{align*}
    x_{k+1} - \Pi(x_0) &= x_{k+1} - \Pi(x_k) \\
    &= x_k + A_I^\dagger (b_I - A_I x_k) - \Pi(x_k) \\
    &= (I - A_I^\dagger A_I) (x_k  - \Pi(x_k)) \\
    &= (I - A_I^\dagger A_I) (x_k  - \Pi(x_0)).
\end{align*}
</p>

For any matrix $B$, the matrix $B^\dagger B$ is a the orthogonal projector onto the range of $B^\top$, so 

<p>
\begin{align*}
    \|x_{k+1} - \Pi(x_0)\|^2 &= \|(I - A_I^\dagger A_I) (x_k  - \Pi(x_0))\|^2 \\
    &= \|x_k  - \Pi(x_0)\|^2 - \|A_I^\dagger A_I(x_k - \Pi(x_0))\|^2.
\end{align*}
</p>

> This is basically what we showed above in a more succinct form, but I think it detracts from the interesting geometry of the problem.

Alternatively, one can expand the quadratic form and use the identity $BB^\dag B = B$ to verify the equality above.
Regardless, one can show that after cycling through all choices of subsets of indices, and assuming that the subsets cover $[1:m]$, we can show that we get a contraction after each epoch, assuming the problem isn't solved yet, and thus we get convergence to the minimizer.

> For a more thorough overview of block Kaczmarz, check out [Needell and Tropp's wonderful paper](https://arxiv.org/abs/1208.3805).