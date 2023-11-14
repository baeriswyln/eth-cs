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
with the quadratic nature of said norm. $l_\infitiy$ can be imagined as a box around the center point. It is also said
to most naturally capture the human vision, and it is simpler to certify. 