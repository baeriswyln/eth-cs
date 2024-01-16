# Reliable and Trustworthy Artificial Intelligence

## General notions

### $l_p$-norm

The similarity of two vectors $x$ and $x'$ is usually captured through an $l_p$ norm. In the following formula,
$\epsilon$ is a threshold of similarity that can be arbitrary.

$$
x \sim x' \; \mathrm{iff} \; ||x-x'||_p < \epsilon
$$

Now, $||x-x'||_p$ specifies the distance between the two vectors, which is computed follows, where $x_i$ is the $i$-th
component of the vector $x$.

$$
||x-x'||_p = \Bigg(\sum_{i=1}^n |x_i-x_i'|^p\Bigg)^{\frac{1}{p}}
$$

Some of the $l_p$ norms are used more often than others. The following list contains important norms.

- $l_0$: adds up all changes. Counts, for example, the number of changed pixels in an image
- $l_2$: Euclidean distance between $x$ and $x'$
- $l_\infty$: maximum noise (change) added to any component of the vectors. The formula can be simplified as

$$
||x-x'||_\infty = max(|x_1-x_1'|, ..., |x_n-x_n'|)
$$

The $l_2$-norm is often used for attacks, but rarely for certification of models due to the difficulties that come
with the quadratic nature of said norm. $l_\infty$ can be imagined as a box around the center point. It is also said
to most naturally capture the human vision, and it is simpler to certify.

## Logic in Deep Learning

Logic can be translated such that it can be "understood" by neural nets. While querying, we can set some parameters
that the output should satisfy. In fact, searching for adversarial examples is a special case of such a query with
logical constraints. Furthermore, it is possible to include logic already in the training process. If applied correctly,
this can improve accuracy.

### Querying

In this case, we start with a trained network, and we look for inputs that satisfy specific properties. The following
standard logic can be used:

- Logical operators $\neg, \neq, \land, \lor, \leq, \geq, <, >, \Rightarrow$
- Functions f: $\mathbb{R}^m \to \mathbb{R}^n$
- Variables, constants
- Arithmetic expressions

As an illustrative example, the following query will be used in this chapter. In words, we search for an input $i$
being classified with label $9$ that is close to a deer ($<25$), but is for sure a new input ($>5$).

\begin{align}
\text{class}\big(NN(i)\big) = 9 \land ||i -\text{deer}||_\infty < 25 \land ||i - \text{deer}||_\infty > 5\\
\bigwedge_{j=1,j\neq9}^k NN(i)[j] < NN(i)[9] \land ||i -\text{deer}||_\infty < 25 \land ||i - \text{deer}||_\infty > 5
\end{align}

This example contains:

- Two functions: $NN$, and $l_\infty$-norm
- Four constants: 9, 5, 25, $\text{deer} \in \mathbb{R}^n$
- One free variabe: $i$

Finding a solution to this constraint is in general not possible through a constraint solver, due to the high complexity
of neural nets. The solution is to convert the constraint $\phi$ into an optimization problem by passing it through a
"translation" function $T$. We state through the following theorem that the optimization problem results to $0$ if and
only iff the constraints are satisfied by the input.

\begin{align}
&\forall x, \quad T(\phi)(x) = 0 \textbf{ iff } x \text{ satisfies } \phi\\
&\forall x, \quad T(\phi)(x) \geq 0
\end{align}

We thus need now a translation function allowing us to solve the above minimization problem through a **gradient-based
optimization**. See the following translation table. (1)
{ .annotate }

1. The translated expressions are not strictly differentiable, but they are good enough in practice. They can be
   solved by various solvers.

| Logical expression         | Translation                          |
|----------------------------|--------------------------------------|
| $t_1 \leq t_2$             | $\max(0, t_1 -t_2)$                  |
| $t_1 \neq t_2$             | $[t_1 = t_2]$                        |
| $t_1 = t_2$                | $T(t_1 \leq t_2 \land t_2 \leq t_1)$ |
| $t_1 < t_2$                | $T(t_1 \leq t_2 \land t_1 \neq t_2)$ |
| $\varphi \lor \psi$        | $T(\varphi) \cdot T(\psi)$           |
| $\varphi \land \psi$       | $T(\varphi) + T(\psi)$               |
| $\varphi \Rightarrow \psi$ | $\neg \varphi \lor \psi$             | 

$[t_1 = t_2]$ evaluates to $1$ if the inside comparison evaluates to **True**.

The (shortened) translation of our original logical constraint function follows. Note that the expansion of the logical
AND series has been shortened for readability.

\begin{align}
&\max(0, NN(i)[1] - NN(i)[9]) + [NN(i)[1] = NN(i[9])]\\
& + ... \\
& + \max(0, ||i -\text{deer}||_\infty - 25) + [||i - \text{deer}||_\infty = 25]\\
& + \max(0, 5 - ||i -\text{deer}||_\infty) + [||i - \text{deer}||_\infty = 5]
\end{align}

### Training

Logical expressions can also be integrated into the training process of a neural net. The constraint that is added to
the network actually leads to an adversarial training - we search for adversarial examples, and include them in the
training process.

The following expression states that we are looking for weights $\theta$ for the network $\rho$ such that the maximum
violation of the property $\phi$ is minimized for any input $z$ when compared against a true image $s$ from the dataset
$D$. $z$ is inside the $l_\infty$-ball of $s$.

$$
\rho(\theta) = E_{s \sim D}[\max_z \neg \phi(z, s, \theta)]
$$

Now, to translate this formula into an optimization problem, we split it into two parts. First, we search for a "bad"
counterexample $bz$, which we then pass to the optimization problem minimizing the effect of this counterexample.

\begin{align}
bz = \arg\min_z\big(T(\neg \phi)(z, s, \rho)\theta\big)\\
\rho(\theta) = E_{s \sim D}[T(\phi)(bz, s, \theta)]
\end{align}

Now, our counterexample $bz$ must stay inside the $l_\infty$-ball $\epsilon$ around $s$. As an example, we look at the
following loss function for the above problem, that gets translated into an
optimization problem. Here, we are searching for a $z$ that has a classification probability of at least $\delta$ for
the class $3$.

\begin{align}
\phi(z, x, \theta) &= ||x - z||_\infty \leq \epsilon \Rightarrow NN_\theta (z)[3] > \delta\\
\phi(z, x, \theta) &= \neg||x - z||_\infty \leq \epsilon \lor NN_\theta (z)[3] > \delta\\
\neg\phi(z, x, \theta) &= ||x - z||_\infty \leq \epsilon \land NN_\theta (z)[3] \leq \delta\\
loss(z,x,\theta) &= \max(0, ||x - z||_\infty - \epsilon) + \max(0, NN_\theta(z)[3] - \delta)
\end{align}

This is generally difficult to optimize for. For this reason, this part is solved as a **Projected Gradient Descent 
(PGD)**. Basically, PGD does one gradient step, and then projects it to the $l_\infty$-ball. This reduces the 
accuracy, but this is not that important in this use case. In our case, the gradient descent is computed for the 
second part of the formula, and then projected to $L_\infty(x, \epsilon)$.

## Regulations

In the past couple of years, first regulations were introduced aiming to preserve a citizen's privacy regarding data
collection done by many companies. In the coming years, bills will be introduced to add further regularization regarding
AI and its use. Already, huge fines have been imposed regarding privacy.

A huge part of the AI regulations require the system to have **fairness**. Discriminating systems are already in use.
This is mostly due to biased data, or data missing out on minorities. Regulations define **sensitive attributes**, some
even define **fairness metrics** making sure that systems do not discriminate.

Further, **explainability** is required - the provider must be able to explain how a certain decision was reached.
This is one point that gets often criticised: even human decisions are not always explainable, and the output of
neural networks cannot be explained, although it is an open field in research.

### GDPR

GDPR (EU's bill for privacy) was not created for AI, but it applies to automated decision-making, thus it has also power
over AI systems. It tries to minimize the data collected, and prevent it from being shared whilst AI systems always
require more data. GDPR defines three data types:

- Personal data: data related to an identified or identifiable natural person
- Pseudo-anonymized data: personal data no longer related directly to a data subject. Additional information is required
  to get to the data subject (e.g. through values replaced by references)
- Anonymized data: personal data that can no longer be related to the data subject. This is the only type of information
  not subject to GDPR

Further, it identifies the following privacy risks.

- Singling out: identifying a person by comparing known information about them with attributes in a different dataset
- Linkability: linking information form different data sets through some attribute (e.g. a person's address)
- Inference: estimating personal data from other attributes

Possible counter measurements to the above risks include _federated learning_, _trusted execution environment_,
_homomorphic encryption_, and _synthetic data_.

Further, the following principles are required by the GDPR.

- Unlearning: a user has the right to be forgotten. We must thus be able to "remove" a training sample from a model.
- Data minimization: the training process must only use the data that is strictly necessary. The formalization of this
  requirement is an open problem.

### EU AI act

The EU AI act assesses risks of AI application, dividing the systems into four risk levels. Based on the risk level,
different requirements are to be met.

- **Unacceptable risk**: social scoring, distorting human behavior, real-time biometric data for law enforcement (face
  recognition)
- **High risk**: critical infrastructure, education access, employment, migration, justice
- **Limited risk**: chatbots, video games, spam filters
- **Minimal or no risk**: most other systems

High risk systems require a risk management system, data governance, technical doc, record-keeping, transparency,
human oversight, and good accuracy and robustness.

### Other AI acts

**AI Bill of rights (US)**: this foundation for upcoming regulations (e.g. Algorithmic Accountability Act) requires
people to be protected from safe and inefficient systems, they should not be discriminated, be protected from abusive
data practices, know about automated systems in use, and have the option to opt out and have access to a human
alternative.

**Digital Services Act (EU)**: not explicit about AI, but AI is also affected. Aimed at large platforms, they are
required to be assessed by an independent third party regarding societal risks, they must have transparency reports
on automated systems, and limit manipulative advertising platforms.

**Recommendation Algorithms Regulation (CN)**: regarding recommendation algorithms, users should have the right for
explanation, must be able to edit tags used for recommendation by themselves, addictive behavior should not be
encouraged, and preferences should not influence a different treatment.

### Emerging issues

- Copyright: if copyrighted data is used for training, the output might be subject to a copyright infringement
- AI generators trained on sensitive data: models are trained on everything. They can then be used to generate sensitive
  content.
- Large scale fake content