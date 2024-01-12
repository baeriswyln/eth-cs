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