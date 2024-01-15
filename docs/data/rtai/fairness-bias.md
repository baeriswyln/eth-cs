# Fairness

Our world is biased towards men of majority communities. This is often reflected in training data as underrepresented
groups, with a lack of demographic diversity. If not treated carefully, AIs and ML models will reflect this bias in
its results, leading to more unfairness.

This chapter focuses on how to combat unfair models through careful modeling and data usage. The task of getting
fair models is task and domain specific.

Sometimes, one tries to combat unfairness by removing some attributes: **fairness by obscurity** (through unawareness).
However, this is not very successful, as it is often possible to predict the removed attribute through other
non-sensitive features.

Based on the features $X \in \mathcal{X}$ describing the data, we want to predict the outcome (target) variable $Y$.
The prediction is done through the learning of a (binary) classifier $h: \mathcal{X} \to \mathcal{Y}$ or a model
$M: \mathcal{X} \to \Delta(\mathcal{Y})$ mapping to probability distributions. The prediction is defined as
$\hat{Y} = h(X)$. Additionally, we introduce a random variable $G$ encoding membership status in a sensitive class.

Note, that a model satisfying group fairness does not automatically satisfy individual fairness. 

## Individual Fairness

Individual fairness (fairness through awareness) defines that two similar inputs produce a similar output. We require
new formal definitions to measure the similarity of inputs and outputs.

- Input similarity: $\mu: \mathcal{X} \times \mathcal{X} \to \mathbb{R}$
- Output distribution similarity: $\phi: \Delta(\mathcal{Y}) \times \Delta(\mathcal{Y}) \to \mathbb{R}$

Additionally, we define the **Lipschitz mapping**: The $(\mu,\phi)$-Lipschitz property is satisfied, if for every $x,y
\in \mathcal{X}$, we have $D\big(M(x), M(y)\big) \leq d(x,y)$.

Individual fairness can be reformulated for binary input and output metrics as $\phi(x, y) \Rightarrow \mu(M(x), M(x'))

The key challenge here is to find suitable functions for $d$ and $D$.

### Issues

### Fair Representation Learning

### LCIFR

### LASSI

## Group Fairness

## Counterfactual Fairness