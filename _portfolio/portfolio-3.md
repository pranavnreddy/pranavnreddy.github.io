---
title: "Performance Estimation for Weakly Convex Functions"
excerpt: "I built an classifier for the MNIST handwritten digits dataset using a linear least squares-based classifier. <br/><img src='/images/WeaklyConvexRates.png'>"
collection: portfolio
---
*Written for ECE 285 with professor [Yang Zheng](https://zhengy09.github.io/index.html). Source code can be accessed [here](https://github.com/pranavnreddy/ECE285Project)*.

I analyzed the worst-case performance of first-order methods on nonsmooth weakly convex functions using the performance estimation framework.
Lacking exact interpolation conditions, I implemented a relaxation using the necessary conditions for weak convexity and obtained (not necessarily tight) upper bounds on the worst-case performance.
The semidefinite program instances were modeled using YALMIP and solved with MOSEK, and the design was left to me. 
The image below shows the worst-case performance of different step sizes over a fixed number of iterations.

![Performance of Different Activations](/images/WeaklyConvexRates.png)

<object class="pdf" 
            data=
"https://pranavnreddy.github.io/files/WeaklyConvexReport.pdf" width="800"
            height="500">
    </object>