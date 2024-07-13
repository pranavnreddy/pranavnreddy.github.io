---
title: 'Solutions to Exercises from _Real Analysis: Modern Techniques and Their Applications_'
date: 2024-07-12
permalink: /posts/2024/07/folland-problems
tags:
  - analysis
  - problems
  - Folland
---

Some selected problems from Gerald Folland's _Real Analysis: Modern Techniques and Their Applications_ that I've done.

## Chapter 1 
### Problem 14

**Problem:** If \\(\mu\\) is a semifinite measure and \\(\mu(E) = \infty\\), prove that for any \\(C > 0\\) there exists \\(F\subseteq E\\) such that \\(C < \mu(F) < \infty\\).

**Solution:** Let \\(\alpha := \sup\\\{\mu(F):F\subseteq E,\, \mu(F)<\infty\\\}\\).
It suffices to show that \\(\alpha = \infty\\).
Suppose \\(\alpha < \infty\\).
Choose \\(G_n\\) such that 
<p>
\begin{equation*}
    \alpha -\frac{1}{n} < \mu(G_n) < \infty.
\end{equation*}
</p>
Note that \\(\mu(G_1\cup\dots\cup G_n) < \infty\\), so 
<p>
\begin{equation*}
    \alpha - \frac{1}{n} < \mu(G_1\cup\dots\cup G_n) \leq \alpha.
\end{equation*}
</p>
Let \\(G:=\bigcup_{n=1}^\infty G_n\\) so that \\(\lim_{n\to\infty}\mu(G_1\cup\dots\cup G_n) = \mu(G)\\) and
<p>
\begin{equation*}
    \alpha \geq \lim_{n\to\infty}\mu(G_1\cup\dots\cup G_n) = \lim_{n\to\infty}(\alpha-\tfrac{1}{n}) = \alpha.
\end{equation*}
</p>
Thus, \\(\mu(G) = \alpha\\).


Let \\(E' = E \,\backslash\, G\\) so \\(\mu(E') = \infty\\).
Because \\(\mu^*\\) is semifinite, there exists \\(F\subseteq E'\\) with \\(0<\mu(F) <\infty\\).
Thus,
<p>
\begin{equation*}
    \alpha = \mu(G) <  \mu(G) + \mu(F)  = \mu(G\cup F) < \infty,
\end{equation*}
</p>
but this contradictions the definition of \\(\alpha\\).
Thus, \\(\alpha = \infty\\).


### Problem 17
**Problem:** If \\(\mu^\*\\) is an outer measure on \\(X\\), and \\(\\\{A_j\\\}\_{j=1}^\infty\\) is a collection of disjoint \\(\mu^* \\)-measurable sets, prove that for any \\(E\subseteq X\\), \\(\mu^\*\left(E\cap\bigcup\_{j=1}^\infty A_j\right) = \sum\_{j=1}^\infty \mu^\*(E\cap A_j)\\).

**Solution:** Firstly, note that if \\(A_1\cap A_2=\varnothing\\) and both are \\(\mu^*\\)-measurable,
<p>
\begin{align*}
    \mu^*(E\cap (A_1\cup A_2)) &= \mu^*((E\cap (A_1\cup A_2))\cap A_1) +\mu^*((E\cap (A_1\cup A_2))\cap A_1^\complement) \\
    &= \mu^*(E\cap A_1) + \mu^*(E\cap A_2).
\end{align*}
</p>
By induction, we have the result for finite collections of disjoint \\(\mu^*\\)-measurable sets.

Then, note that for all \\(k\in\mathbb{N}\\),
<p>
    \begin{align*}
    \sum_{j=1}^k\mu^*(E\cap A_j) = \mu^*\left(E\cap\bigcup_{j=1}^k A_j\right) 
    \leq \mu^*\left(E\cap\bigcup_{j=1}^\infty A_j\right)  
    \leq \sum_{j=1}^\infty\mu^*(E\cap A_j).
    \end{align*}
</p>
Letting \\(k\to\infty\\) we obtain the desired result.


