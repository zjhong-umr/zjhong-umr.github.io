---
layout: default
category: machine learning & deep learning
---

> Some notes

## 预测阶段

$$x_0: $$ 输入的残缺的图像，$$x: $$ 输出图像

$$R^{\phi}: $$ 输出图像中的残缺块，$$\phi(x): $$ 转换映射

$$h(x, R): $$ 返回$$x$$中的$$R$$，$$h(\phi(x), R^{\phi}): $$ 返回 $$\phi(x): $$ 中的 $$R^{\phi}$$

$$f(x): $$ content prediction network, $$t: $$ texture network

填充结果由下式求得：$$\tilde{x} = \underset{x}{\operatorname{argmin}} E_c(f(x), f(x_0)) + \alpha E_t(\phi_t(x), R^{\phi})+\beta \gamma(x)$$

论文中 $$\alpha, \beta$$ 均设为 $$5e^{-6}$$

其中

$$E_c$$ 为Holistic content constraint(全局内容限制) $$E_c(f(x), f(x_0)) = \|f(x)-f(x_0)\|_{2}^{2}$$

$$E_t$$ 为Local texture constraint(局部内容限制)，对缺失块中的每个patch(大小3x3)，要在hole之外找到最相似的一块，然后再计算similarity

$$E_t(\phi_t(x), R) = \frac{1}{R^{\phi}} \sum\limits_{i \in R^{\phi}} \| h(\phi_t(x), P_i) - h(\phi_t(x), P_{nn(i)}) \|_2^2 $$

$$nn(i) = \underset{j \in \mathcal{N}(i) \land j \notin R^{\phi}}{\operatorname{argmin}} \| h(\phi_t(x), P_i) - h(\phi_t(x), P_{j}) \| $$

// 疑惑1：论文中说 nn(i) 能够从其中一篇引用文献中的CNN中的一层求得，但是直观上看公式实际上挺简单的，应该不需要那个mrf-cnn来计算，然而源代码中貌似是使用了大量的mrfcnn来计算，所以实际上计算方法是什么呢？没有读过那篇论文有点不能理解

TV-loss: $$\gamma (x) = \underset{i, j}{\operatorname{\sum}} ((x_{i, j+1} - x_{i,j}^2) + (x_{i+1,j} - x_{i,j}^2)) $$

// 疑惑2：这里也不理解TV loss的写法，为什么是一次减去二次呢？

# 训练Content Prediction Network

$$x_g$$ 为真实图像。

损失函数分两部分，一部分为L2损失，一部分为对抗损失。 

l2 loss: $$ L_{l2} (x, x_g, R) = \| f(x) - h(x_g, R) \|_2^2 $$

adversarial loss: $$\underset{D}{\operatorname{\max}} E_{x \in \mathcal{X}}[log(D(h(x_g, R))) + log(1-D(f(x)))] $$

// 疑问3：adversarial discriminator的实现来构造对抗损失

joint: $$L = \lambda L_{l2}(x, x_g, R) + (1-\lambda)L_{adv}(x, x_g, R) $$

论文中设置 $$\lambda$$ 为 0.999。

对于训练集处理，限制为中心固定的正方形，而网络的输出也为缺失部分的填补。

另外，论文中使用ELU来代替RELU

// 而这部分的网络跟context encoder的实现基本完全相同

# Texture network

Texture network是直接将残缺图像输入VGG-19，并从中提取relu3_1和relu4_1层，根据论文的描述，尽管minimization problem的优化公式中只有一个texture term，论文实际上使用了两层，因此应该是分别计算，而不用取平均值。

取两层的好处在于，优化速度更快，而且结果更加准确。

# 优化过程

0. 对于joint loss minimization problem，使用有限内存的BFGS算法
1. 首先对于挖去中间部分的图像 $$x_0$$，其缺失部分用图像的像素均值来填充
2. 输入Content Prediction Network，得到 $$f(x_0)$$
3. 根据梯度开始调整hole-filling result $$\tilde{x}$$
4. $$\tilde{x}$$ 填入 $$x_0$$ 对应位置
5. 重复2，3，4