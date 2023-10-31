# Robustness

AIs have typically issues where they make false classifications due modified inputs. Such modifications can happen on
data directly, or on the data that is being captured. All in all, it gets more and more important to have AIs that
have a certain level of certified robustness giving some securities to the user. This is especially important for
security-relevant applications.

However, it is generally hard to certify models. And often, certification and improved robustness come with a certain
cost regarding the model's performance, especially if those certifications are required to be scalable.

## Attacks

AI models have generally weaknesses and can be fooled by attackers. Adding a small amount of noise to an image can
for example lead an image recognition model to wrong conclusions, or adding tape at certain spots on a stop sign can
lead models to recognise it as a different sign.

### Adversarial attacks

In adversarial attacks, we generally view two types of attacks:

- **Targeted Attack** where the attacker aims to classify the input as a **specific label** which differs from the
  correct label.
- **Untargeted Attack** where the aim is to classify the input as some label that is not the correct one.

The attacks can be further split into **white box** attacks where the attacker knows the complete model including
parameters and the architecture, and **black box** attacks where the attacker only knows the architectures but has no
information about the parameters.

The following chapters only discuss white box attacks.

#### Fast Gradient Sign Method (FGSM)

This attack uses the gradient descent of the classification model. The loss function of the gradient descent is
computed, and the added/removed from the input to form a modified output that should be classified with some other
label. The loss function is, however, modified before being added/removed: only the sing of the values is looked at,
not the exact value. This lead to better results than taking the gradient itself.

First, the perturbation is computed. This is slightly different for the targeted (T) and untargeted (U) mode. In T, the
loss function is computed for the target label **3**, whereas in U, the original label **s** is used.

$$
\eta_{t/s} = \epsilon \cdot sign(\nabla_xloss_{t/s}(x))
$$

where $\epsilon$ a small value indicating how large the perturbation should be, and $sign(g)$ returning -1 if $g < 0$, 0
if $g = 0$, or 1 if $g > 0$. When applied to an image, $x_i$ is a pixel of the image.

As the output of the loss function is passed through the sign function, and then multiplied by $\epsilon$, the output is
guaranteed to stay in the range $x \pm \epsilon$. The original paper had a single iteration of this algorithm. If the
perturbation did not change the output, the next input was looked at. The algorithm is designed to be fast and simple.

- **Targeted**: $x' = x-\eta_t$ modifies the input by **minimising the loss** for the label $t$.
- **Untargeted**: $x' = x+\eta_s$ modifies the input by **maximising the loss** for the original label $s$.

#### PGD

### Adversarial defenses

## Certification

### Certified defense

## Relaxation

### Box

### MILP

### DeepPoly

### DiffPoly
