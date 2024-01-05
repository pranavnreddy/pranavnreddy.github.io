---
title: 'First Post'
date: 2024-01-04
permalink: /posts/2024/01/automorphisms-of-u1
tags:
  - group theory
  - notes
  - lie groups
---
I was thinking on an interesting problem and decided to write it up for myself.

The unit circle in $\mathbb{R}^2$ can be identified as $U(1)$, the multiplicative subgroup of complex numbers of the form $e^{i\theta}$, where $\theta$ is a real number.
A natural question is to identify the automorphism group of the so-called "circle group."
However, since this is a topological group, we can make use of the additional structure to simplify the classification.

The proof proceeds as follows:
1. Isomorphisms preserve the orders of elements.
2. The subgroup of all roots of unity is precisely the elements of  finite order.
3. 1 and 2 imply that the subgroup of all roots of unity is a characteristic subgroup.
4. The subgroup of all roots of unity is dense in the circle group.
5. 3 and 4 imply that we need only to look at how each $n$-th root is mapped to another $n$-th root. By density, the automorphism will be determined by its action on the dense subset.

# 1. Isomorphisms preserve orders of elements
This section is mainly included for completeness.
Let $G$ be a group and $g\in G$ have order $n$.
Let $H$ be a group and $\phi\colon G\to H$ be an isomorphism.
Then,
$$\phi(g)^n = \phi(g^n) = \phi(e) = e.$$
Suppose $m< n$, then if $\phi(g)^m = e$ this would imply that $g^m = e$, a contradiction.
Therefore, the order of $\phi(g)$ is $n$.

Suppose $g \in G$ has infinite order.
Then, if $\phi(g)^n = e$ for some $n$, it would follows that 
$$e = \phi(g)^n = \phi(g^n)$$
and therefore $g^n = e$, which is a contradiction.
Therefore, $\phi(g)$ must have infinite order.

# 2. The subgroup of all roots of unity is precisely the elements of  finite order.
We will prove this in the slightly more general context of the multiplicative group of $\mathbb{C}$, denoted $\mathbb{C}^\times$.

It is clear by definition that an $n$-th root of unity has finite order, since by definition it is the set of solutions to $x^n = 1$.

Let $z\in\mathbb{C}^\times$ be an element of order $n$.
If $|z| < 1$, then $|z^{n}| < |z| < 1$, so $z$ cannot have finite order.
Similarly, $|z|>1$ is not possible.
Therefore, we can say that $|z| = 1$, and write $z = e^{i\theta}$ for some $\theta\in\mathbb{R}$.
Since $z$ has order $n$, it follows that $e^{in\theta} = 1$.
Therefore, $n\theta = 2\pi k$, for some $k\in\mathbb{Z}$.
It follows that $\theta = \frac{2\pi k}{n}$ and is therefore an $n$-th root of unity.

# 3. The group of all roots of unity is a characteristic subgroup of $\mathbb{C}^\times$
This follows from the more general fact that in an abelian group, the set of all elements with finite order is a subgroup.

Suppose $G$ is an abelian group.
Then, let $H$ be the set of all elements of finite order.

The identity element is clearly in this set.

Suppose $a,b\in H$, and let $o(a)$ and $o(b)$ be the order of $a$ and $b$, respectively.
Then, $$(ab)^{o(a)o(b)} = (a^{o(a)})^{o(b)}(b^{o(b)})^{o(a)} = e.$$
Thus, $ab\in H$.

Suppose $a\in H$.
Then, 
$$e = e^n = (aa^{-1})^n = a^n(a^{-1})^n = (a^{-1})^n.$$
So $a^{-1}\in H$.

Since isomorphisms preserve order, it follows that $H$ is a characteristic subgroup of $G$.

# 4. The subgroup of all roots of unity is dense in the circle group
We will prove the the set of $2^n$-th roots of unity is dense.
The distance between consecutive $2^n$-th roots of unity is given by the length of the base of an isoceles triangle, which is given by $d_n = 2\sin\left(\frac{2\pi}{2^n}\right)$.
Taking the limit as $n\to\infty$, we can see that $d_n\to 0$.
Since any element of the circle group, denoted $z$, is between two $2^n$-th roots of unity for all $n$, we can conclude that the distance between $z$ and a $2^n$-th root of unity is less than $d_n$, which goes to 0.

# 5. Automorphisms on roots of unity
We have shown that roots of unity map to other roots of unity of the same order.
Moreover, it suffices to only look at the action on this subgroup, since it is dense.
We will induct on $n$, the degree of the root.

Firstly, we note that the maps $z\mapsto \bar{z}$, complex conjugation, is an automorphism of the circle group.

Denote the arc going counter clockwise from $a$ to $b$ by $A_{a,b}$.

Let $a = e^{i\frac{2\pi k}{n}}$ and $b = e^{i\frac{2\pi(k+1)}{n}}$.
Our inductive statement will be that for all $n$-th roots of unity, $\phi$ is either the identity or the conjugation map.
If $\phi$ is the identity, then $\phi(A_{a,b}) = A_{a,b}$.
If $\phi$ is conjugation, then $\phi(A_{a,b}) = A_{\bar{b},\bar{a}}$.

Let $n=2$. 
Then the corresponding roots of unity are 1 and -1.
Let $\phi$ be an automorphism of the circle group, so that
$$\phi(1) = \phi((-1)\cdot(-1)) = \phi(-1)^2.$$
Therefore, $\phi(-1) = \pm 1$, but since $\phi$ is injective $\phi(-1)$ cannot be $1$.
Thus, $\phi(-1) = -1$.
Then, it can be seen that $\phi(-1) = \overline{-1} = -1$ and $\phi(1) = \bar{1} = 1$.
Thus, the base case holds, since clearly $\phi(A_{1,-1}) = A_{1,-1}$ or $\phi(A_{1,-1}) = A_{-1,1}$.

Suppose that the statement holds for the $n$-th roots of unity.
Then, let $z = e^{i\frac{2\pi k}{n+1}}$ be an $(n+1)$-th root of unity.
It follows that $z$ lies on an arc connecting two $n$-th roots of unity, denoted $a$ and $b$, as in the inductive hypothesis.
Moreover, $z$ is the unique $(n+1)$-th root of unity on this arc.
If $\phi$ is the identity on the $n$-th roots of unity, then 
$\phi(A_{a,b}) = A_{a,b}$, and therefore $\phi(z) = z$.
If $\phi$ is the conjugation map on the $n$-th roots of unity, then $\phi(A_{a,b}) = A_{\bar{b},\bar{a}}$, so $z\mapsto\bar{z}$.
Then, by continuity, the desired properties of the image of $A_{z, z'}$ for distinct $(n+1)$-th roots of unity follow.

Thus, the map $\phi$ is either the identity on all roots of unity or the conjugation map on all roots of unity.

# 6. $\mathrm{Aut}(U(1))\cong \mathbb{Z}_2$
Denote by $I$ the identity mapping and by $C$ the conjugation map.
It is clear that the map that sends $C\mapsto 1$ and $I\mapsto 0$ is a group homomorphism, since $C\circ C =  I$.
This is also a bijection, and thus we have that $\mathrm{Aut}(U(1))\cong \mathbb{Z}_2$.