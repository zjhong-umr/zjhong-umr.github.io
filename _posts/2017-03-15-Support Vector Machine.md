---
layout: default
category: machine learning & deep learning
---

> 关于支持向量机的基本数学原理

## Kernel Methods

$$\kappa (\vec{x}, \vec{y}) = \phi^{\mathrm T}(x) \phi(y)  $$

### Radial Basis Function

$$\kappa (\vec{x}, \vec{y}) = exp(\frac{-\lVert \vec{x} - \vec{y} \rVert^{2}}{2\sigma})$$ 

**WLOG**, set $$\sigma = 1$$:

$$\kappa (\vec{x}, \vec{y}) = exp(-\frac{\lVert \vec{x} - \vec{y} \rVert^{2}}{2}) = exp(-\frac{\lVert \vec{x} \rVert^2}{2}) exp(-\frac{\lVert \vec{y} \rVert}{2})exp(\vec{x}^{\mathrm T}\vec{y})$$ 

*Taylor Fomulation*:

$$exp(\vec{x}^{\mathrm T}\vec{y}) = \sum\limits_{j=0}^{\infty} \frac{1}{j!}(\vec{x}^{\mathrm T}\vec{y})^j $$

Then let $$k$$ be the demensions of $$\vec{x}$$ and $$\vec{y}$$:

$$\kappa(\vec{x}, \vec{y}) = \sum\limits_{j=0}^{\infty}(\sqrt{\frac{1}{j!}}exp(-\frac{\lVert \vec{x} \rVert^2}{2j}) \sqrt{\frac{1}{j!}}exp(-\frac{\lVert \vec{y} \rVert}{2j}) (\vec{x}^{\mathrm T}\vec{y}))^{j}$$

Thus we have,

$$\kappa (\vec{x}, \vec{y}) = = \sum\limits_{j=0}^{\infty} \sum\limits_{\sum_i^k n_i = j}\sqrt{\frac{1}{j!}}exp(-\frac{\lVert \vec{x} \rVert^2}{2j}) {\dbinom{j}{n_1, n_2,...,n_k}}^{\frac{1}{2}}x_1^{n_1} x_2^{n_2}...x_k^{n_k} \sqrt{\frac{1}{j!}}exp(-\frac{\lVert \vec{y} \rVert^2}{2j}) {\dbinom{j}{n_1, n_2,...,n_k}}^{\frac{1}{2}} y_1^{n_1} y_2^{n_2}...y_k^{n_k}$$


Therefore,

$$\phi (\vec{x}) = \sqrt{\frac{1}{j!}}exp(-\frac{\lVert \vec{x} \rVert^2}{2j}) {\dbinom{j}{n_1, n_2,...,n_k}}^{\frac{1}{2}}x_1^{n_1} x_2^{n_2}...x_k^{n_k})_{j=0,...,\infty, \sum_{i=1}{^k}n_i=j}$$

PS: The infinite dimension is not only determined by the $$j$$ but also the combination.
































