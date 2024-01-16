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

- Input similarity: $\phi: \mathcal{X} \times \mathcal{X} \to \mathbb{R}$
- Output distribution similarity: $\mu: \Delta(\mathcal{Y}) \times \Delta(\mathcal{Y}) \to \mathbb{R}$

Additionally, we define the **Lipschitz mapping**: The $(\mu,\phi)$-Lipschitz property is satisfied, if for every $x,y
\in \mathcal{X}$, we have $\mu\big(M(x), M(y)\big) \leq \phi(x,y)$.

Individual fairness can be reformulated for binary input and output metrics as $\phi(x, y) \Rightarrow \mu(M(x), M(x'))$

### Robustness

The key challenge is to find suitable functions for $\mu$ and $\phi$, as well as trying to train a model such that the
individual fairness is satisfied, without the accuracy being sacrificed. And what is also important: how can we
_guarantee_ a certain degree of fairness?

We define the following metrics:

- $\phi(x, y) := (x - y)^TS(x-y)$ with $S$ a symmetric covariance matrix
- $\mu\big(M(x), M(y)\big) := [M(x) \neq M(y)]$
- Mahalanobis norm $||x||_S := \sqrt{x^TSx}$

Using the Lipschitz property, we find the following ($L > 0$ being the Lipschitz constant).

\begin{align}
\mu\big(M(x), M(y)\big) &\leq L\phi(x,y)&\\
[M(x) \neq M(y)] &\leq L||x - x'||_S^2 &&\quad \forall y\\
||x - y||^2_S &< \frac{1}{L} \Rightarrow M(x) = M(y) &&\quad \forall y\\
M(x) &= M(x+\delta) &&\quad \forall \delta \text{ with } ||\delta||_S^2 < \frac{1}{L}\\
M(x) &= M(x+\delta) &&\quad \forall \delta \text{ with } ||\delta||_S < \frac{1}{\sqrt{L}}
\end{align}

The last line can be rephrased as: $M$ needs to be robust to adversarial perturbations of magnitudes up to
$\frac{1}{\sqrt{L}}$ under the Mahalanobis norm. Using a suitable similarity metric, we can now use known techniques
for robustness certification.

### Fair Representation Learning

In fair representation learning, the training process is partitioned into three parts. This leads to a modular system,
often being more efficient than alternatives. It can also be used if the data provider is not interested in providing
a fair data set. However, the performance tradeoff is harder to manage, and it might lead to overconfidence in the
fairness, and we have no provable certificates.

The **Data Regulator** defines fairness criteria and data sources. He does also the audits of the results.

The **Data Producer** takes the requirements of the regulator, and applies them on the dataset using his encoder
$f_\theta: \mathbb{R}^n \to \mathbb{R}^k$.

Finally, the **Data Consumer** uses the encoded data, and trains his predictors $h_\psi: \mathbb{R}^k \to \mathbb{R}^o$.
The design of this process allows the consumer to completely ignore the fairness aspect.

### LCIFR

As the name suggests, the **Learning certified individually fair representations** (LCIFR) framework adds
certifiability to the fairness, while retaining the modularity.

The **Data Regulator** defines logical constraints for the input similarity, which are mostly domain-specific. This
_captures categorical features exactly_. These similarity metrics lead to a set $S_\phi(x)$ of inputs that are all
similar to $x$. He requires all values from this set to be classified equally.

The **Data Producer** translates the logical constraints for minimization, and then trains his encoder $f_\theta$ to
map similar points close together. We have the encoder constraint $\phi(x,y) \Rightarrow ||f_\theta(x) - f_\theta(y)||
_\infty \leq \delta$ that we need to translate into a logical constraint. Using known logical transformations, we arrive
at ($\omega(x,y) = ||f_\theta(x) - f_\theta(y)||_\infty \leq \delta)$

$$
L(\neg \phi) \cdot L(\omega)
$$

using the function $L$ for translation of logical functions. Further, we can have

$$
L(\omega)(x,y) = \max\{||f_\theta(x)- f_\theta(y)||_\infty - \delta, 0\}
$$

These translations result in a **differentiable loss function**, and can be used in the following functions where we
search for a good encoder $f_\theta$ that maximizes the probability that the fairness is satisfied ($\phi \Rightarrow
\omega$). In a first step, we require a counterexample.

$$
x^* = \arg\min_{y \in S_\phi(x)}L(\neg(\phi \Rightarrow \omega))(x,y)
$$

Then, we find the parameters $\theta$ that minimize the loss at $x^*$ regarding the logical constraints.

$$
\arg\min_\theta E_{x\sim D}[L(\phi \Rightarrow \omega)(x,x^*)]
$$

Additionally, it is also possible to train a classifier $q$ making sure that the fairness does not cause a too large
hit in accuracy. Using a classification loss $L_C$ and a fairness loss $L_F$ (obtained through above steps), we
arrive at the following final optimization problem.

$$
\arg\min_{f_\theta, q} E_{x,y}[L_C\Big(q\big(f_\theta(x)\big), y\Big) + \gamma L_f\big(x, f_\theta(x)\big)]
$$

The output is a bounding box of radius $\delta$ that are provably similar. The **Data Consumer**'s task is now pretty
easy. He must classify all values in this region to the same output using a standard optimization.

$$
\arg\min_\psi E_{z \sim D_z} [\max_{\pi\in[\pm \delta]} L_C\big(h_\psi(z + \pi), y)]
$$

The remaining issues regarding this approach are that they are not usable on _high-dimensional_ datasets such as images,
as the similarity cannot be measured through the features (pixels). Additionally, the techniques do not scale to
real-world models.

### LASSI

The **Latent space smoothing for individually fair representations** frameworks extends on LCIFR by making it suitable
for high-dimensional inputs. Concretely, it allows the definition of attribute vectors for features of a data entry.
Such an attribute vector could be a specific hair color. Using a generative model, such an attribute can be varied
continuously to specify similar inputs.

The data producer uses **center smoothing**(1) to cluster similar points according to the defined feature vectors with a
high probability of $1 - \alpha_{cs}$. The data consumer uses **randomized smoothing** to certify that all points within
a certain radius get classified to the same output with a high probability of $1-\alpha_{rs}$.
{ .annotate }

1. Extension to randomized smoothing for multidimensional regression computing a sound upper bound $d_{cs}$ on the
   $l_2$-ball of the smoothed function in the ball of radius $\epsilon$ with a center point $z$. This ball contains at
   least half of the points $r_i \sim R(z + N(0, \sigma^2_{cs}I))$.

One downside of this technique is that the fairness certificate depends heavily on the generative model. The similarity
set might not include all points perceived as similar to the original input $x$.

## Group Fairness

In group fairness, we are more concerned on fairness on a distributional level. This means that we could be unfair
towards one specific individual without losing group fairness. In general, we have three different constraints:

- **Demographic parity**: The probability for a specific output is equal among two groups differing in a protected
  attribute. $\mathbb{P}(\hat{Y} = 1 | G = 0) = \mathbb{P}(\hat Y = 1 | G = 1)$
- **Equal odds**: The probability for a specific output is equal among two groups, depending on the true output.
  $\mathbb{P}(\hat{Y} = 1 | Y=0, G = 0) = \mathbb{P}(\hat Y = 1 | Y=0, G = 1)$ and
  $\mathbb{P}(\hat{Y} = 1 | Y=1, G = 0) = \mathbb{P}(\hat Y = 1 | Y=1, G = 1)$
- **Equal opportunity**: The probability for a specific output is equal among two groups, where the predicted output
  equals the true output. $\mathbb{P}(\hat{Y} = 1 | Y=1, G = 0) = \mathbb{P}(\hat Y = 1 | Y=1, G = 1)$

There are three general techniques to guarantee the group fairness:

- **Pre-processing** (Fair Representation Learning FRL): The data is transformed such that any classifier trained on
  said data is faire. Any model can be
  used to make prediction, and there is no need to know the sensitive attributes. However, accuracy might be overly
  sacrificed, and malicious predictors might try hard to discriminate. We must thus check for the worst case classifier.
- **In-training**: Brings the best tradeoff between fairness and accuracy, and we need no access at test time to
  protected attributes. However, It is very expensive, requires access to the training pipeline, and the solution is
  individual to each model. A possibility is to add a _demographic parity_ constraint to the loss calculation of the
  model.
- **Post-processing**: This efficient method works for any classifier. However, it requires test-time access to
  sensitive attributes, and it is not very flexible regarding the fairness/accuracy tradeoff. A possible approach is to
  modify the thresholds for the classification, making sure that all protected groups are treated equally.

The techniques discussed in the following chapters all focus on the _pre-processing_ technique. The following notations
well be of importance.

- Data $(x, s) \in \mathbb{R}^d \times \{0,1\}$ with $x\in X$ the input and $s$ the sensitive group membership
- Encoder $f: \mathbb{R}^d \times \{0,1\} \to \mathbb{R}^{d'}$, resulting in a fair version $f(x,s) = z \in Z$
- Classifier $g: \mathbb{R}^{d'} \to \{0,1\}$ trained for binary prediction $(z \to y)$
- Adversary $h: \mathbb{R}^{d'} \to \{0,1\}$ trying to guess the sensitive group membership $(z \to s)$
- $Z_0$, $Z_1$ the distributions of the encoded data regarding the sensitive group membership.
- $p_0(z) := P(z | s = 0)$, $p_1(z) := P(z | s = 1)$ the densities of sensitive group membership.

### LAFTR

With LAFTR, we train an encoder, classifier and adversary at the same time. The goal is to learn an encoder that leads
to representations $z$ allowing for good accuracy by the classifier but makes it hard for $h$ to predict the sensitive
group membership.

!!! warning "Quality"

    LAFTR is a theoretically-principled FRL, as we are not sure to find the best adversary. 

    The resulting models are sometimes good, but the min-max optimisation (non-convex) is often not solved optimally, 
    leading to suboptimal results. Additionally, the adversary model is fixed and we only train the weights. It is 
    possible, that there exist other architectures leading to better adversaries. **The fairness is overestimated**.

LAFTR defines the following optimisation problem. The adversary found here is not guaranteed to be the best possible.

$$
\min_{f,g}\max_{h\in\mathcal{H}} \Big(\mathcal{L}_{cl \;f}\big(f(x,s),g\big)-\gamma \cdot \mathcal{L}_{adv}\big(f(x,s)
,h\big)\Big)
$$

The key challenge here is to upper-bound unfairness with an optimal adversary. This requires a **soft unfairness
measure** based on the demographic parity (DP) hard-constraint, leading to the **DP-distance**. $0$ means perfect
fairness, while $1$ means maximum unfair.

$$
\Delta^{DP}_{Z_0,Z_1}(g) := \Big| \mathbb{E}_{z\sim Z_0}g(z) - \mathbb{E}_{z\sim Z_1} g(z)\Big|
$$

Additionally, we define the **balanced accuracy** $BA \in [0.5,1]$ of an adversary, which is a group-normalized accuracy
especially useful for imbalanced datasets. If $h$ always predicts sensitive group 1: $BA = 0.5$. In the following
formula, $h$ is either $1$ or $0$, and thus selects one of the two probabilities.

$$
BA_{Z_0,Z_1}(h) := \frac{1}{2} \int_Z\Big(p_0(z)(1-h(z)) + p_1(z)h(z)\Big)dz
$$

Based on this metric, we now search for the **optimal adversary** $h^*$. This adversary selects always the higher
probability group: $h^* = \mathbb{1}\{p_1(z) \geq p_0(z)\}$. Using all the above definitions, we can now define an
upper bound DP-distance based on the optimal adversary:

$$
\Delta^{DP}_{Z_0,Z_1}(g) \leq 2 \cdot BA_{Z_0,Z_1}(h^*)-1
$$

### FNF

The "Fair Normalizing Flows"  approach tries to find some provable upper bound using two normalizing flows $f_0$ and 
$f_1$. These flows are learned to encode $Z_0$ and $Z_1$. We know the original distributions, and try to map original 
densities onto similar densities $p_0(z)$ and $p_1(z)$.

The technique provides a provable upper bound on unfairness for any downstream classifier, and the training process
is quite efficient reducing the adversary's success.

!!! info "Normalizing Flows"

    Starting from some known distribution $q$ with known density $q(x)$, we apply a composition of invertible functions
    $z = f(x)$ (the normalizing flow) to transform $q$ into a learned distribution $p$. Using _change of variables_, 
    we can compute the density of the new distribution at $p(z)$.

This process allows us to define a provable upper bound $T$

$$
\Delta^{DP}_{Z_0,Z_1}(g) \leq 2 \cdot BA_{Z_0,Z_1}(h^*)-1 \leq T(w.p. 1-\varepsilon)
$$

1. For each saple $x \in X$, compute the density $q_{x[s]}(x)$
2. Apply the encoder $z=f_{x[s]}(x)$ and use flows to get $p_{z[s]}(z)$
3. Use $p_0(z)$ and $p_1(z)$ to estimate $h^*$ and upper-bound its _BA_ with a provability of $1-\varepsilon$
4. Upper-bound the DP-distance using above inequality

The above steps are not yet sufficient, as we want $T$ to be as small as possible. In the training process, we try to
minimize the **KL Divergence**, which is equivalent in saying that we are trying to bring both distributions $p_0$ and
$p_1$ as close together as possible. The loss computed for the KL divergence is combined with the standard
classification loss, making sure that we are not sacrificing accuracy.

!!! warning "Assumptions"

    The densities for $q_0(x)$ and $q_1(x)$ are only estimated. The certificates given by FNF are only valid if the
    following holds.
    
    1. The distance between estimated and real densities $q$ are provable
    2. Data distribution is known

    In reality, neither of these holds in most cases, which is an important limitation. 

### FARE

With **Fairness with Restricted Encoders**, we improve on FNF by having a provable upper-bound unfairness with practical
certificates. This is done by skipping the estimation of $q$, and directly compute an upper bound for $p_0(z)$.

As its name suggests, we use a _restricted encoder_, mapping each input on one of $k$ representations $z_i$. The encoder
is defined as $f: \mathbb{R}^d \times \{0,1\} \to \{z_1,...,z_k\}$. Using this encoder, we can compute the probabilities
$p_0(z)$ and $p_1(z)$ by looking at the group represented by $f(z)$. 

!!! info "Restricted Encoder"

    Currently, there is no known "best" restricted encoder. One in use by the paper is a modified decision tree 
    classifier. Instead of simply using the Gini index, the classifier does the following: minimize entropy on the 
    output label, while maximising the entropy on the protected attribute (having an even distribution on both branches
    of the node)

The provable upper bound can now be computed as

$$
BA_{Z_0,Z_1}(h^*) = \sum_{i=1}^k P(z = z_i) \cdot \max \Bigg( \big(\frac{1}{2}P(s=0)\big) \cdot P(s=0|z=z_i),
\big(\frac{1}{2}P(s=1)\big) \cdot P(s=1|z=z_i)\Bigg)
$$

The upper bound of this expression is computed in three steps based on a finite dataset. Each step results in a constant
that can be used in the following steps. 

1. **Bound base rates**: $0.5P(s=0)$ and $0.5P(s=1)$ using the training set  (error $\varepsilon_b$)
2. **Bound per-cell balanced accuracy**: bound the $\max$ expression to $c_i \leq t_i$ (error $\varepsilon_c$)
3. **Bound final sum**:  $\sum P(z=z_i) \cdot t_i \leq S$ (error $\varepsilon_s$)

This gives us a total error of $\varepsilon = \varepsilon_b + \varepsilon_c + \varepsilon_s$ and an upper bound for the 
DP-distance of

$$
\Delta^{DP}_{Z_0,Z_1}(g) \leq 2S-1 = T(w.p. 1- \varepsilon)
$$