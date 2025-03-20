---
title: 'Solutions to Exercises from _Real Analysis: Modern Techniques and Their Applications_'
date: 2024-07-12
permalink: /posts/2024/07/folland-problems
tags:
  - analysis
  - problems
  - Folland
toc: true
---

Some selected problems from Gerald Folland's _Real Analysis: Modern Techniques and Their Applications_ that I've done.

{% include toc %}

# Chapter 1 
## Problem 14

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
Let \\(G:=\bigcup_{n=1}^\infty G_n\\) so that
<p>
\begin{equation*}
    \mu(G) = \lim_{n\to\infty}\mu(G_1\cup\dots\cup G_n) = \lim_{n\to\infty}(\alpha-\tfrac{1}{n}) = \alpha.
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


## Problem 17
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



## Problem 18
**Problem:** Let $\mathcal{A}\subset P(X)$ be an algebra, $\mathcal{A}\_\sigma$ the collection of countable unions of sets in $\mathcal{A}$, and $\mathcal{A}\_{\sigma\delta}$ the collection of countable intersections of sets in $\mathcal{A}\_\sigma$. Let $\mu\_{0}$ be a premeasure on $\mathcal{A}$ and $\mu^*$ the induced outer measure.
<ol type="a">
<li> For any $E\subset X$ and $\varepsilon > 0$ there exists $A\in \mathcal{A}_\sigma$ with $E\subset A$ and $\mu^*(A) \leq \mu^*(E) + \varepsilon$.
</li>
<li> If $\mu^{*}(E) < \infty$, then $E$ is $\mu^{*}$-measurable if and only if there exists $B\in A_{\sigma\delta}$ with $E\subset B$ and $\mu^{*}(B\setminus E) = 0$.
</li>
<li> If $\mu_0$ is $\sigma$-finite, the restriction $\mu^{*}(E) < \infty$ in (b) is superfluous.
</li>
</ol>

**Solution**:
<ol type="a" style = "li::marker {
  font-weight: bold;
}">
    <li>
        <p>
        From the definition, 
        \begin{align*}
            \mu^*(E) = \inf \left\{\sum_{n=1}^\infty \mu_0(A_n) : A_n\in\mathcal{A}, \, E \subseteq \bigcup_{n=1}^\infty A_n\right\}.
        \end{align*}
        Simply applying the definition of infimum yields a collection $\{A_n\}_{n=1}^\infty\subseteq\mathcal{A}$ with $E \subseteq \bigcup_{n=1}^\infty A_n \in\mathcal{A}_\sigma$ and $\sum_{n=1}^\infty \mu_0(A_n) \leq \mu^*(E) + \varepsilon$.
        Then, by Proposition 1.13, we have
        \begin{align*}
            \mu^*\left(\bigcup_{n=1}^\infty A_n\right) \leq \sum_{n=1}^\infty \mu^*(A_n) =\sum_{n=1}^\infty \mu_0(A_n) \leq \mu^*(E) + \varepsilon.
        \end{align*}
        </p>
    </li>
    <li>
        <p>
            Suppose $E$ is $\mu^*$-measurable.
        </p>
        <p>
            From part (a), choose $B_n\in A_{\sigma}$ such that $E\subseteq B_n$ and $\mu^*(B_n)\leq\mu^*(E)+\frac{1}{n}$.
            Then, 
            \begin{align*}
                \mu^*(E)+\frac{1}{n}\geq \mu^*(B_n) = \mu^*(B_n\cap E) + \mu^*(B_n\cap E^\complement) = \mu^*(E) + \mu^*(B_n \setminus E).
            \end{align*}
            Because $\mu^*(E)<\infty$, we have $\mu^*(B_n \setminus E)\leq \frac{1}{n}$.
            Let $B = \bigcap_{n=1}^\infty B_n$, so that $\mu^*(B\setminus E) \leq \mu^*(B_n\setminus E) \leq \frac{1}{n}$ for all $n$.
            Thus, $\mu^*(B\setminus E) =0$, and since $E\subseteq B\in A_{\sigma\delta}$, we have completed the proof of the forwards direction.
        </p>
        <p>
            Suppose there exists $B\in A_{\sigma\delta}$ such that $E\subseteq B$ and $\mu^*(B\setminus E) = 0$.
            Let $A\subseteq X$ be any set.
            Note that $\mu^*(A\cap E) \leq \mu^*(A\cap B)$.
            Also,
            \begin{align*}
                \mu^*(A\cap B) &= \mu^*(A\cap E \cup A\cap (B\setminus E)) \leq \mu^*(A\cap E) + \mu^*(A\cap (B\setminus E)) = \mu^*(A\cap E).
            \end{align*}
            Thus, $\mu^*(A\cap E) = \mu^*(A\cap B)$.
        </p>
        <p>
            Similarly, 
            \begin{align*}
                \mu^*(A\cap E^\complement) &= \mu^*(A\cap B^\complement \cup A\cap (E^\complement\setminus B^\complement)) \leq \mu^*(A\cap B^\complement) + \mu^*(A\cap (B\setminus E)) = \mu^*(A\cap B^\complement).
            \end{align*}
            Since $\mu^*(A\cap B^\complement)\leq \mu^*(A\cap E^\complement)$, we have $\mu^*(A\cap B^\complement) = \mu^*(A\cap E^\complement)$.
            We also used the fact that $E^\complement\setminus B^\complement = B\setminus E$.
        </p>
        <p>
            Lastly, by Proposition 1.13 every set in $\mathcal{A}$ is $\mu^*$-measurable. By Theorem 1.11 (Carathéodory's Theorem), the set of $\mu^*$-measurable sets forms a $\sigma$-algebra.
            Thus, $B$ is $\mu^*$-measurable.
            Combining all of the results,
            \begin{align*}
                \mu^*(A) = \mu^*(A\cap B) + \mu^*(A\cap B^\complement) = \mu^*(A\cap E) + \mu^*(A\cap E^\complement).
            \end{align*}
            Since $A$ was arbitrary, we have that $E$ is $\mu^*$-measurable.
            This completes the proof of the reverse direction.
        </p>
    </li>
    <li>
        <p>
            Let $\{B_n\}_{n=1}^\infty$ be a collection of sets in $\mathcal{A}$ such that $X = \bigcup_{n=1}^\infty B_n$ and $\mu_0(B_n)<\infty$ for all $n$, where $X$ is the ambient space.
        </p>
        <p>
            Now, suppose $E$ is $\mu^*$-measurable.
            Define $E_n:=E\cap \bigcup_{i=1}^n B_i$ so that
            \begin{align*}
            \mu^*(E_n) = \mu^*\left(E\cap \bigcup_{i=1}^n B_i\right) \leq \mu^*\left( \bigcup_{i=1}^n B_i\right)  \leq \sum_{i=1}^n\mu^*(B_i) = \sum_{i=1}^n\mu_0(B_i) < \infty.\end{align*}
            Note that we use Proposition 1.13 in the last equality.
        </p>
        <p>
            By part (a), choose $A_n^{(k)}\in\mathcal{A}_\sigma$ so that $E_n\subseteq A_n^{(k)}$ and 
            \begin{align*}
            \mu^*(A_n^{(k)}) \leq \mu^*(E_k)+ \frac{1}{2^{k+n}}.\end{align*}
            By Theorem 1.11 (Carathéodory's Theorem) and Proposition 1.13, $E_n$ is $\mu^*$-measurable,
            Repeating a computation from part (b), we find that $\mu^*(A_n^{(k)} \setminus E_k) \leq \frac{1}{2^{k+n}}$.
            Then, $E \subseteq \bigcup_{k=1}^\infty A_n^{(k)}$, and
            \begin{align*}
                \mu^*\left(\bigcup_{k=1}^\infty A_n^{(k)}\setminus E\right) &= \mu^*\left(\bigcup_{k=1}^\infty (A_n^{(k)}\setminus E)\right) \\
                &\leq \sum_{k=1}^\infty \mu^*\left(A_n^{(k)}\setminus E\right) \\
                &\leq \sum_{k=1}^\infty \mu^*\left(A_n^{(k)}\setminus E_k\right) \\
                &\leq \frac{1}{2^n}\sum_{k=1}^\infty \frac{1}{2^{k}} \\
                &= \frac{1}{2^n}.
            \end{align*}
            Define $A = \bigcap_{n=1}^\infty \bigcup_{k=1}^\infty A_n^{(k)}$, so that
            \begin{align*}
            \mu^*(A\setminus E) \leq \mu^*\left(\bigcup_{k=1}^\infty A_n^{(k)}\setminus E\right) \leq \frac{1}{2^n}\end{align*}
            for all $n$.
            Thus, $\mu^*(A\setminus E) = 0$.
            This proves the forwards direction.
        </p>
        <p>
            Suppose there exists $G\in \mathcal{A}_{\sigma\delta}$ such that $E\subseteq G$ and $\mu^*(G\setminus E) = 0$.
            Let $A\subseteq X$ be any set.
            Define $E_n = E\cap\bigcup_{i=1}^n B_i$, and $G_n = G\cap\bigcup_{i=1}^n B_i$.
            Note that $G_n\in\mathcal{A}_{\sigma\delta}$ and that for any sets $A,B$, and $C$,
            \begin{align*}
            (A\cap C)\setminus (B\cap C) = (A\cap C)\cap (B\cap C)^\complement =
            (A\cap B^\complement\cap C)\cup (A\cap C^\complement\cap C)=(A\setminus B)\cap C.\end{align*}
            Applying this fact, we find 
            \begin{align*}
                \mu^*(G_n\setminus E_n) &= \mu^*\left((G\setminus E)\cap\bigcup_{i=1}^n B_i \right) \leq \mu^*(G\setminus E) = 0.
            \end{align*}
            By part (b), $E_n$ is $\mu^*$-measurable.
            Since $E = \bigcup_{n=1}^\infty E_n$ and from Theorem 1.11 (Carathéodory's Theorem), we have that $E$ is $\mu^*$-measurable.
            This completes the proof of the reverse direction.
        </p>
    </li>
</ol>



# Chapter 5
AKA: I got bored with measure theory AKA functional ``analysis'' (algebra).

## Problem 57
**Problem:**
Suppose $H$ is a Hilbert space and $T\in L(H,H)$.
<ol type="a">
<li> There exists a unique $T^*\in L(H,H)$ such that $\langle Tx, y\rangle = \langle x, T^*y \rangle$ for all $x,y\in H$. $T^*$ is called the <strong>adjoint</strong> of $T$
</li>
<li> $\| T^*\| = \|T\|$, $T^** = T$, $\|T^*T\| = \|T\|^2$ $T\mapsto T^*$ is conjugate-linear, and $(ST)^* = T^*S^*$.
</li>
<li> $(\im T )^\perp = \ker T^*$ and $(\ker T)^\perp = \mathrm{cl}(\im T^*)$
</li>
<li> $T$ is unitary if and only if $T$ is invertible and $T^{-1} = T^*$.
</li>
</ol>

**Solution**:
<ol type="a">
<li>
    <p>
    Define $T^\top:H^*\to H^*$ by $T^\topf(x) = f(Tx)$.
    Let $V:H\to H^*$ be the conjugate linear isomorphism.
    Then $T^* = V^{-1}T^\top V$ and
    \begin{align*}
    \langle x, T^*y\rangle = \langle x, V^{-1}T^\dag V y\rangle = \langle x, V^{-1}(Vy\circ T)\rangle = (Vy\circ T)(x) = \langle Tx, y\rangle .
    \end{align*}
    </p>
    <p>
    Suppose $T^*_1, T^*_2$ are such that for all $x,y\in H$,
    \begin{align*}
    \langle Tx, y\rangle = \langle x, T_1^*y\rangle = \langle x, T_2^*y\rangle.
    \end{align*}
    Then,
    \begin{align*}
    0 = \langle Tx, y\rangle -\langle Tx, y\rangle = \langle x, T_1^*y\rangle - \langle x, T_2^*y\rangle = \langle x, (T_1^*-T_2^*)y\rangle.
    \end{align*}
    Since $x$ was arbitrary, $(T_1^*-T_2^*)y = 0$.
    But since $y$ was also arbitrary, this implies that $T_1^*-T_2^* = 0$.
    </p>
</li>
<li> 
    <p>
    Note that 
    \begin{align*}
    \langle T^*x, y\rangle = \overline{\langle y,T^*x\rangle} = \overline{\langle Ty,x\rangle} = \langle x,Ty\rangle.
    \end{align*}
    Thus, $(T^*)^* = T$.
    </p>
    <p>
    Then,
    \begin{align*}
    \|Tx\|^2 = \langle Tx, Tx\rangle = \langle x, T^*Tx\rangle  \leq \|x\|\|T^*Tx\|\leq \|T^*\|\|x\|\cdot \|Tx\|
    \end{align*}
    Thus, $\|Tx\|\leq \|T^*\|\|x\|$, so $\|T\|\leq \|T^*\|$.
    Using the fact that $(T^*)^* = T$, we see that $\|T\| = \|T^*$.
    </p>
    <p>
    Also,
    \begin{align*}
    \|T^*Tx\|^2 = \langle T^*Tx, T^*Tx\rangle = \langle TT^*Tx, Tx\rangle \leq \|T\|^2\|T^*Tx\|\|x\|.
    \end{align*}
    Thus, $\|T^*T\|\leq\|T\|^2$.
    Moreover,
    \begin{align*}
    \|Tx\|^2 = \langle Tx, Tx\rangle = \langle x, T^*Tx\rangle  \leq \|T^*T\|\|x\|^2.
    \end{align*}
    Thus, $\|T\|^2\leq \|T^*T\|$ so $\|T\|^2 = \|T^*T\|$.
    </p>
    <p>
    Also,
    \begin{align*}
    \langle x, (\overline{a}S^*+\overline{b}T^*)y\rangle = \langle x,\overline{a}S^*y\rangle + \langle x, \overline{b}T^*y\rangle = \langle \overline{a}x,S^*y\rangle + \langle \overline{b}x, T^*y\rangle &= \langle \overline{a}Sx, y\rangle + \langle \overline{b}Tx, y\rangle \\
    &= \langle (aS+bT)x, y\rangle.
    \end{align*}
    Thus, $({a}S+{b}T)^* = \overline{a}S^*+\overline{b}T^*$.
    </p>
    <p>
    Lastly,
    \begin{align*}
    \langle x, T^*S^*y\rangle = \langle Tx, S^*y\rangle = \langle STx, y\rangle,
    \end{align*}
    so $(ST)^* = T^*S^*$.
    </p>
</li>
<li> 
<p>
Note that 
\begin{align*}
  R(T)^\perp = \left\{ y\in H : \langle Tx, y\rangle = 0 \ \forall \ x\in H \right\} &= \left\{ y\in H : \langle x, T^*y\rangle = 0 \ \forall \ x\in H \right\} \\
  &= \left\{ y\in H :  T^*y = 0 \right\} \\
  &= N(T^*).
\end{align*}
We used the fact that $\langle x, y\rangle =0$ for all $x$ if and only if $y = 0$.
</p>
<p>
Now, applying the previous result and the fact that $(T^*)^* = T$, we see that $R(T^*)^\perp = N(T)$.
Thus, $(R(T^*)^\perp)^\perp = N(T)^\perp$.
By problem 56, $(R(T^*)^\perp)^\perp$ is the smallest closed subspace containing $R(T^*)$.
Since $R(T^*)$ is a subspace, and the closure of a subspace is still a subspace, we have that $(R(T^*)^\perp)^\perp = \overline{R(T^*)}$.
Thus, $N(T)^\perp = \overline{R(T^*)}$.
</p>
</li>
<li>
    <p>
    Suppose $T$ is unitary.
    Then, for all $x,y$,
    \begin{align*}
    \langle x, y\rangle = \langle Tx, Ty\rangle = \langle x, T^*Ty\rangle.
    \end{align*}
    This implies that $0 = \langle x, T^*Ty -y\rangle$ for all $x$.
    Choosing $x = T^*Ty -y$ shows that $T^*Ty -y = 0$.
    Thus, $T^*Ty = y$ for all $y$, so $T^*T = I$.
    But then, since $T$ is unitary and thus invertible,
    \begin{align*}
    T^{-1} = T^*T T^{-1} = T^*.
    \end{align*}
    </p>
    <p>
    Now suppose $T^* = T^{-1}$.
    Then,
    \begin{align*}
    \langle Tx, Ty\rangle = \langle x, T^*Ty\rangle = \langle x,y\rangle.
    \end{align*}
    This shows that $T$ is unitary.
    </p>
</li>
</ol>


## Problem 67 (Mean Ergodic Theorem)
**Problem:**
Let $U$ be a unitary operator on a Hilbert space $H$.
Let $M = \{x \in H: Ux = x\}$ (these are the fixed points of $U$).
Show that if $S_n = \frac{1}{n}\sum_{k=0}^{n-1} U^k$, then $S_n$ converges to $\Pi_M$ (the orthogonal projection onto $M$) in the strong operator topology.

**Solution:**
We need to first show that $\Pi_M$ exists.
