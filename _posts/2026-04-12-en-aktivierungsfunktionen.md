---
layout: default
title: "Activation Functions: An Analysis of Gradients and Representations in Deep Networks"
description: "Why deep neural networks collapse without nonlinearity: A technical analysis of vanishing gradients, ReLU, SiLU, GeLU, and the geometry of Softmax."
category: data-science
lang: en
permalink: /en/data-science/activation-functions/
de_url: /data-science/aktivierungsfunktionen/
---

# Activation Functions: An Analysis of Gradients and Representations in Deep Neural Networks

The development of artificial intelligence, and deep learning in particular, is strongly tied to the mathematical mechanisms that make it possible to model complex, nonlinear relationships in data. The literature often emphasizes the number of parameters or the computational power of modern GPUs, but the true "intelligence" of a neural network lies in its **activation functions**.

These functions are often regarded as tools for "squashing" values into an interval $[0,1]$. However, their mathematical role protects against mathematical meaninglessness. Without the introduction of nonlinearity, even a model with trillions of parameters would degenerate into a simple, collapsed linear regression — incapable of solving even basic logical problems like the XOR operation.

---

### The Mathematical Necessity of Nonlinearity

In linear algebra, the composition of two linear mappings is necessarily again a linear mapping. This can be expressed mathematically through matrix multiplication: if one layer of a network is defined by the transformation $y = W_1x + b_1$ and the subsequent layer by $z = W_2y + b_2$, the overall mapping becomes:

$$z = W_2(W_1x + b_1) + b_2 = (W_2W_1)x + (W_2b_1 + b_2)$$

The product of two matrices $W_2W_1$ is a new matrix $W_{eff}$, and the combined biases yield a new bias vector $b_{eff}$. Consequently, an arbitrarily deep network without nonlinear activation functions collapses into a shallow model that can only define linear decision boundaries in a high-dimensional space.

The activation function $\phi$ breaks this chain by inserting a function between the layers that is neither additive nor homogeneous:

$$z = \phi(Wx + b)$$

This simple yet brilliant function allows the network to bend and fold the input space so that complex structures — such as edges in images, semantic relationships in text, or logical dependencies — can be modeled. The specific choice of $\phi$ critically determines the stability of gradients during the training process.

| Concept | Mathematical Representation | Effect Without Nonlinearity |
| :--- | :--- | :--- |
| Linear layer | $f(x) = Wx + b$ | No curvature of the feature space possible |
| Composition | $f(g(x)) = W_2(W_1x + b_1) + b_2$ | Reduction to a single layer |
| Representation limit | $y = \sum w_i x_i$ | Inability to learn XOR or nonlinear manifolds |
| Gradient flow | $\frac{\partial y}{\partial x} = W$ | Constant gradients, no complex adaptations |

---

### Sigmoid and Tanh

The idea: a biological neuron either fires an electrical impulse or remains inactive. To model this behavior mathematically, S-shaped functions were introduced that simulate a transition between two states.

![Sigmoid and Tanh activation functions](/assets/images/data-science/aktivierungsfunktion/Bild_1.PNG)

### The Sigmoid Function and Gradient Saturation

The sigmoid function is defined as:

$$\sigma(x) = \frac{1}{1 + e^{-x}}$$

It maps the real numbers to the interval $(0, 1)$. This was advantageous since the outputs could be directly interpreted as probabilities, making it the standard choice for the output layer of binary classification models. However, a significant disadvantage lies in its derivative:

$$\sigma'(x) = \sigma(x)(1 - \sigma(x))$$

The maximum value of this derivative is $0.25$ (at $x = 0$). When an error gradient is backpropagated through a deep network with $n$ layers, these values multiply. Since each factor is at most $0.25$, the gradient shrinks exponentially ($0.25^n$), causing the weights in the early layers of the network to no longer be updated — the **Vanishing Gradient Problem**.

Moreover, sigmoid units suffer from **saturation**. When the input $x$ takes on very large positive or negative values, the function asymptotically approaches $1$ or $0$, and the slope becomes extremely flat. In this state, the optimizer loses all directional information.

A further issue is that sigmoid outputs are **not zero-centered**. Since all outputs are positive, the gradients for the weights of a layer always have the same sign during backpropagation, which can lead to inefficient parameter optimization.

### The Hyperbolic Tangent Function (Tanh)

To address the lack of zero-centering, the tanh function was introduced:

$$\tanh(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$$

It shifts the output range to $(-1, 1)$, so that the average activation of a layer lies closer to zero. This accelerates convergence compared to sigmoid. Nevertheless, the fundamental **Vanishing Gradient** problem remains, as tanh also saturates in the outer regions. Both functions are computationally expensive, as they require the evaluation of exponential functions.

| Property | Sigmoid | Tanh |
| :--- | :--- | :--- |
| Output range | $(0, 1)$ | $(-1, 1)$ |
| Mathematical form | $(1 + e^{-x})^{-1}$ | $\frac{e^{2x} - 1}{e^{2x} + 1}$ |
| Zero-centered | No | Yes |
| Max. derivative | $0.25$ | $1.0$ |
| Saturation | At large positive/negative values | At large positive/negative values |

---

### The ReLU Activation Function

The breakthrough for deep learning came through simplification. The **Rectified Linear Unit (ReLU)** is defined as:

$$f(x) = \max(0, x)$$

It replaced the complicated S-curves with a piecewise linear function.

### Why Is ReLU Important in Deep Learning?

An often-overlooked advantage of ReLU is the generation of **sparsity**. Since all negative input values are set exactly to zero, many neurons are inactive at any given time. Mathematically, this sparsity means that the network uses only the most relevant features for a given input, increasing efficiency and providing a form of implicit regularization that can reduce the risk of overfitting.

### Drawback: Exploding Gradients and Dying ReLU

A major disadvantage of ReLU is that it is unbounded from above. Activation values and thus gradients can become extremely large in deep networks, leading to numerical instability and the **Exploding Gradient Problem**. This often requires careful weight initialization and techniques such as batch normalization.

A more serious problem is the **"Dying ReLU" problem**. When the weights of a neuron are adjusted such that it receives negative input for all data points in the training set, it permanently outputs zero. Since the derivative of ReLU is zero for negative values, this neuron can never be updated again — it is mathematically "dead." In some cases, up to 50% of neurons in a network can become inactive during training, massively limiting the model's capacity.

---

### Overcoming Dead Zones: LeakyReLU and ELU

To solve the problem of dying neurons, researchers developed variants that allow information flow even in the negative region.

### LeakyReLU and PReLU

**LeakyReLU** introduces a small slope $\alpha$ for negative values:

$$f(x) = \begin{cases} x & \text{if } x > 0 \\ \alpha x & \text{if } x \le 0 \end{cases}$$

Typically, $\alpha$ is set to $0.01$.

![LeakyReLU activation function](/assets/images/data-science/aktivierungsfunktion/Bild_2.PNG)

This "leak" ensures that even inactive neurons retain a minimal gradient and can thus migrate back into the active region through future updates.

An extension is **Parametric ReLU (PReLU)**, where the parameter $\alpha$ is not fixed but is optimized as a weight during training. This allows the network to decide for itself how strongly certain features are negatively weighted. Experiments show that PReLU often provides higher accuracy and faster convergence, albeit at the cost of an increased risk of overfitting.

### Exponential Linear Unit (ELU)

The **Exponential Linear Unit (ELU)** is defined as:

$$f(x) = \begin{cases} x & \text{if } x > 0 \\ \alpha(e^x - 1) & \text{if } x \le 0 \end{cases}$$

In the negative region, ELU asymptotically approaches $-\alpha$. This curve causes the average activation of a layer to lie closer to zero, similar to tanh, but without the vanishing gradient problem on the positive side. Due to the exponential saturation in the negative region, ELU is also more robust to noise and outliers in the data. Studies show that networks with ELU often learn faster and generalize better than those with conventional ReLUs.

| Function | Behavior for $x < 0$ | Main Advantage | Disadvantage |
| :--- | :--- | :--- | :--- |
| ReLU | Exactly $0$ | Maximum sparsity | Permanent dying |
| LeakyReLU | $\alpha x$ (linear) | Prevents dead neurons | Manual choice of $\alpha$ |
| PReLU | $\alpha_i x$ (learnable) | Adaptive flexibility | Overfitting risk |
| ELU | $\alpha(e^x - 1)$ | Smooth transition, robust | Computationally expensive |

---

### SiLU (Swish) and GeLU

With the rise of big data and increasingly deep transformer architectures, simple activation functions reached their limits.

### SiLU (Swish): Discovered by AI

The **Sigmoid Linear Unit (SiLU)**, or Swish function, was developed by Google DeepMind using reinforcement learning and a search over thousands of function combinations. It is defined as:

$$f(x) = x \cdot \sigma(x) = \frac{x}{1 + e^{-x}}$$

![SiLU (Swish) activation function](/assets/images/data-science/aktivierungsfunktion/Bild_3.PNG)

**Why is SiLU better than ReLU?**

1. **Non-Monotonicity:** SiLU has a small "dip" below zero around $x \approx -1.28$ before asymptotically approaching zero. This means it exhibits different dynamics for small negative values than for large negative values. This behavior helps the network learn more complex filter structures that would be lost through zeroing.

2. **Smoothness:** While ReLU has a hard kink at zero, SiLU is fully differentiable. This mathematical smoothness produces a significantly more stable loss landscape. SiLU smooths these regions, considerably stabilizing gradient computation for optimization methods like Adam.

![Comparison ReLU vs SiLU loss landscape](/assets/images/data-science/aktivierungsfunktion/Bild_4.PNG)

3. **Self-Gating:** The function uses its own value to control the information flow. The input value $x$ is "gated" by its own sigmoid transformation. This resembles the gating mechanisms in LSTMs but is mathematically much more efficient to implement.

### GeLU: The Transformer Standard

The **Gaussian Error Linear Unit (GeLU)** is the standard activation in models such as BERT, GPT-2, and most modern transformer architectures. It is defined as:

$$f(x) = x \cdot P(X \le x) = x \cdot \Phi(x)$$

where $\Phi(x)$ is the cumulative distribution function of the standard normal distribution.

![GeLU activation function](/assets/images/data-science/aktivierungsfunktion/Bild_5.PNG)

GeLU combines the properties of ReLU (sparsity) with the smoothness of SiLU, but offers a probabilistic interpretation: a value is weighted by how likely it is to be significant under a Gaussian distribution.

In practice, GeLU and SiLU are functionally very similar. However, GeLU shows slightly better performance on NLP tasks, as it more precisely captures the stochastic properties of language data. Both functions allow the network to not simply discard negative information, but to weight it in a differentiated manner.

---

### Softmax and the Geometry of Probability

While the functions described above serve almost exclusively in the hidden layers for feature extraction, **Softmax** plays a special role in the output layer for classification tasks. Softmax is not an element-wise function but a multivariate mapping:

$$\text{Softmax}(z_i) = \frac{e^{z_i}}{\sum_{j=1}^{K} e^{z_j}}$$

It considers the relationship of all neurons in the layer relative to each other and ensures that the sum of all outputs is exactly $1$.

![Softmax visualization](/assets/images/data-science/aktivierungsfunktion/Bild_6.PNG)

![Softmax geometry](/assets/images/data-science/aktivierungsfunktion/Bild_7.PNG)

### The Problem of Overconfidence

A critical problem of modern deep networks is **deterministic overconfidence**. Due to their immense capacity and the use of functions like ReLU, models tend to heavily favor one class (e.g., with a probability of $0.999$), even when confronted with data far outside their training distribution (Out-of-Distribution, OOD). This pseudo-certainty is dangerous in safety-critical domains such as medicine or autonomous driving.

### Temperature Scaling as a Corrective

To calibrate the probability distribution, **Temperature Scaling** is often employed. Here, the logits $z$ are divided by a scalar $T$ before the softmax computation:

$$\text{Softmax}_T(z_i) = \frac{e^{z_i / T}}{\sum_{j=1}^{K} e^{z_j / T}}$$

A $T > 1$ softens the distribution and increases entropy, causing the model to output more moderate probabilities under uncertainty. Since $T$ is optimized on a separate validation set, accuracy is preserved while confidence values become significantly more realistic.

---

### Curvature and Self-Normalizing Networks

### The Significance of the Second Derivative

The ReLU function has a second derivative that is zero almost everywhere ($f''(x) = 0$ for $x \neq 0$). This means that the loss landscape of a ReLU network consists of flat planes colliding at sharp kinks. Functions like SiLU or GeLU, on the other hand, have non-trivial curvature. This is invaluable for modern second-order optimizers, which use information about local curvature to more precisely calculate not only the direction of descent but also the optimal step size, which can massively accelerate convergence.

### SELU and Self-Normalizing Neural Networks

The **Scaled Exponential Linear Unit (SELU)** was developed to enable "Self-Normalizing Neural Networks" (SNNs). By choosing specific constants ($\alpha \approx 1.67326$ and $\lambda \approx 1.0507$), SELU induces a mapping that stabilizes the mean and variance of activations across hundreds of layers. Using the **Banach Fixed-Point Theorem**, it was proven that activations close to zero mean and unit variance converge through the layers to exactly this stable fixed point. This theoretically makes techniques like batch normalization unnecessary.

| Mathematical Concept | Application | Relevant Function |
| :--- | :--- | :--- |
| First derivative | Backpropagation (Gradient Descent) | All (esp. ReLU) |
| Second derivative | Curvature analysis, Newton methods | SiLU, GeLU |
| Fixed-point iteration | Automatic normalization | SELU |
| Lipschitz continuity | Robustness to perturbations | Sigmoid, Tanh |

---

### Conclusion and Outlook

The choice of activation function is not arbitrary, but follows clear architectural standards based on the balance between computational efficiency, gradient stability, and representational power.

1. **Hidden layers in LLMs/Transformers:** SiLU and GeLU are the standard in transformer architectures. Their smoothness and non-monotonicity enable these large models to detect extremely subtle patterns in trillions of data points while maintaining a stable gradient flow.

2. **CNNs:** ReLU remains the first choice for mobile applications and real-time image processing due to its speed and natural sparsity.

3. **Classification outputs:** Softmax remains indispensable, but must be supplemented in modern applications by techniques such as temperature scaling to limit dangerous overconfidence.

4. **Specialized networks:** SELU offers a solution for very deep MLPs where one wishes to forgo complex normalization layers.

The future of activation functions likely lies in further dynamization. New approaches such as "Expanded Gating" (xSiLU, xGELU) are already experimenting with adapting gating regions individually for each MLP block during training. Without the constant refinement of these tiny mathematical components, the revolution of large language models and the vision of artificial general intelligence would never have been possible.
