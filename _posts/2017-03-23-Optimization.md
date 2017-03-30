---
layout: default
category: machine learning & deep learning
---

> 基本凸优化方法

## Newton Method

一维情况下：

对于 $$f(x)$$ 根据泰勒公式近似有
$$f(x_0+\Delta x) \approx \frac{f(x_0)}{0!}+\frac{f'(x_0)}{1!}\Delta x + \frac{f''(x_0)}{2!}\Delta^2x$$

要求最小值，则$$f'(x_0+\Delta x) = 0$$

于是有，$$f'(x_0)+f''(x_0)\Delta x = 0 \rightarrow \Delta x = -\frac{f'(x_0)}{f''(x_0)}$$

于是对于初始的$$x_0$$（若是凸函数，即可任意），可以直接迭代：

$$ x_{n+1} = x_n - {f''(x_n)}^{-1}{f'(x_n)} $$

多维情况下：

$$f(x_0+\Delta x) \approx f(x_0) + \nabla f(x_0)^T \Delta x + \Delta x^T H(f)(x_0) \Delta x$$

$$f'(x) \rightarrow \nabla f(x)$$

$$f''(x) \rightarrow H(f)(x)$$

$$ x_{n+1} = x_n - {H(f)(x)}^{-1}{\nabla f(x)} $$


- 优点：second order algorithm，下降的比较快
- 缺点：Hessian矩阵计算时间复杂度，占用空间复杂度都比较高，多维数据性能下降比较厉害


---


