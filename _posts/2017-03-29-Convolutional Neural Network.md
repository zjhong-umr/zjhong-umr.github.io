---
layout: default
category: machine learning & deep learning
---

> 卷积神经网络

## Convolution

$$I' = \sum_{u, v} I(x-u, y-v)g(u,v)$$

- 注意是x-u，y-v，倒着来的
- zero-ping: 在边界情况下，可能会越界，使用zero-padding即是普通地在数组的左方和上方加一圈0，使得卷积结果的大小跟原来一样。

