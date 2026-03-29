---
layout: default
title: "Data Science: Shannon Entropy and Gini Impurity"
description: "A brief look into the mathematics behind decision trees."
category: data-science
lang: en
permalink: /en/data-science/shannon-entropy/
de_url: /data-science/shannon-entropie/
---

# Data Science: Shannon Entropy and Gini Impurity

Learning with decision trees is an important and simultaneously simple method in Artificial Intelligence. One of the many advantages of decision trees is that they require very little data preparation. In particular, no scaling or centering of features is necessary. The basic idea is to generate knowledge from data. In this case, however, not as a function, but as a tree graph. This helps to achieve greater understanding and better interpretability of the results.

But how does an algorithm decide where to best split the data? This is where two central concepts come into play: **Shannon Entropy** and **Gini Impurity**.

---

### 1. Shannon Entropy

Simply put, the entropy $H$ is a measure of the uncertainty of a probability distribution. The greater the uncertainty, the greater the number of encoded bits we need to represent the information.

Let's look at two extreme cases for $n$ classes:
* **Minimal uncertainty:** The result occurs with certainty. The distribution looks like this: $\vec{P}=(1,0,0,...,0)$.
* **Maximal uncertainty:** There is an absolute uniform distribution of probabilities.

#### Derivation of the formula
To measure the information content, we ask ourselves: How many bits are needed for encoding?

* If a result occurs with certainty, we need 0 bits.
* With a maximal uniform distribution, we need $x=\log_{2}(n)$ bits, because all $n$ places are needed. This results from the relationship $2^{x}=n$.

Since with a uniform distribution the probability $p$ for each event is exactly $\frac{1}{n}$, we can rearrange this to $n=\frac{1}{p}$. Substituting this into our logarithm, we get the required bits for a single event:

$$x=\log_{2}\left(\frac{1}{p}\right)=-\log_{2}(p)$$

To calculate the general case for an entire distribution, we form the expected value $H$ for the number of bits. To do this, we weight the calculated bits with their respective probability of occurrence $p_{i}$.

This results in the famous formula for Shannon Entropy:

$$H(\vec{p})=-\sum_{i=1}^{n}p_i\log_2(p_i)$$

---

### 2. Gini Impurity as an Alternative

Besides entropy, there is another very popular measure to evaluate how well a decision tree splits the data: Gini Impurity.

While entropy asks about the information content, Gini Impurity directly measures the "purity" of a node in the tree. A node is considered absolutely pure if all data points contained in it belong to the same category.

The formula for the Gini Impurity at the $k$-th node looks like this:

$$G_{k}=1-\sum_{i=1}^{n}p_{k,i}^2$$

*(Where $p_{k,i}$ is the proportion of data points of category $i$ at node $k$.)*

#### A short calculation example
Imagine we are evaluating data for a skier and checking the feature "Snow Distance $\le 100$ km". In one branch of this node, exactly 4 "Yes" answers and 0 "No" answers end up. 

We calculate the Gini Impurity for this branch:

$$G_{\le100}=1-\left(\frac{4}{4}\right)^2-\left(\frac{0}{4}\right)^2=1-1-0=0$$

A Gini Impurity of 0 means: The node is perfectly pure! The tree has split optimally at this point.

---

### Conclusion: How do algorithms use this in practice?

Modern machine learning libraries like `scikit-learn` in Python use, for example, the so-called CART algorithm (Classification and Regression Tree) to train decision trees. 

This algorithm splits the training data step by step so that the cost function is minimized. It therefore always looks for exactly the feature and the threshold value that generates the purest subsets. In the case of classification, the cost function minimizes the weighted Gini Impurity (or alternatively the entropy) of the left and right branch:

$$J(i,t_{i})=\frac{m_{left}}{m}G_{left}+\frac{m_{right}}{m}G_{right}$$
