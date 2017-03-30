---
layout: default
category: machine learning & deep learning
---

> 最大熵模型与EM算法

# EM Algorithm

### Objective: 
  **Maximum likelihood** $$l(\theta) = \sum_{i=1}^{n}\ln P(x_i;\theta)$$

### Jensen's Inequality:

$$E(f(x)) \ge f(EX)$$ iff $$f'(x) \le 0$$

### Procedure:

$$l(\theta)  = \sum_{i=1}^{n}\ln P(x_i;\theta) = \sum_{i=1}^{n}\sum_{z}\ln Q_i(z_j) \frac{P(x_i;\theta)}{Q_i(z_j)} \ge  \sum_{i=1}^{n}\sum_z Q_i(z_j)\ln \frac{P(x_i;\theta)}{Q_i(z_j)}$$ 

where $$Q_i(z_j) = \frac{P(x_i, z_j; \theta)}{P(x_i)}$$

## Optimization

### M-Step:

$$Q_i(z_j) := \frac{P(x_i, z_j; \theta)}{P(x_i)}$$

### E-step:

$$\theta = argmax_{\theta} \; \sum_{i=1}^{n}\sum_z Q_i(z_j)\ln \frac{P(x_i;\theta)}{Q_i(z_j)} $$


---

# Gaussian Mixture Model

**Maximum log-likelihood**:

$$l(\theta) = \sum \ln p(x)$$

$$p(x) = \sum_{k=1}^{K} \pi_k N(x|\mu_k, \Sigma_k)$$

$$\pi_k = p(z_k = 1)  \longrightarrow  \sum_{k=1}^{K} \pi_k = 1$$

$$N(x|\mu_k, \Sigma_k) = (2\pi)^{-\frac{N}{2}}|\Sigma|^{-\frac{1}{2}} \exp(-\frac{1}{2}(x-\mu)^T \Sigma^{-1} (x-\mu))$$

$$p(x) = p(x|z)p(z) = \sum_{k=1}^{K} p(x|z_k)p(z_k) $$ 

$$ \longrightarrow \sum_{k=1}^{K} \pi_k p(x|z_k) $$































