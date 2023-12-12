# Algorithms and Datastructures

This course discusses a series of well-known algorithms and datastructures that are used throughout the world of 
computer science to solve easy and hard problems.

The algorithms and datastructures are analysed regarding their complexity (most notably time complexity).

## Comparing and measuring algorithms

We always try to find faster algorithms to decrease the computation time (sometimes we also look for algorithms taking
up less memory). Most importantly, the algorithms must run correctly. As a developer, we can't influence the hardware
or the input given ot an algorithm. The only possibility to make a program faster is by using a better algorithm. 

The following chapters are mostly not looking on improvements of constant factors (e.g. an algorithm which is twice
as fast), but compares algorithms running in $O(n!)$ against $O(n)$ for example. To make such comparisons, we use the
asymptotic notation which then leads to the big-O notation.

### Asymptotic notation (Big-O notation)

To measure the complexity of a problem, we often use the asymptotic notation. This notation gives us an idea of how
difficult the algorithm is compared to the input size. We have two functions $g,f: \mathbb{N} \to \mathbb{R}^+$. In our
purpose, the function computes the number of elementary operations executed in an algorithm, depending on an input size
$n$. However, in the simple asymptotic notation, this input can signify anything. 

The function $f$ **grows asymptotically faster than** $g$, if $\lim_{n \to \infty} \frac{g(n)}{f(n)} = 0$. For 
such computations, **L'Hôpital's rule** is often very useful. It states that the limit of the division of two functions
is equal to the division of their derivatives: 

$$
\lim_{n \to \infty} \frac{g'(n)}{f'(n)}
$$

This also leads to the conclusion that only the term which is growing fastest is important for the asymptotic growth, 
the other terms can be neglected. 

This is where the $O$-notation comes in. It describes the complexity of an algorithm (the number of elementary 
operations) while ignoring constant factors, and states that one function is growing **at most as fast** as some other
function. For example, $g \leq O(f) \Leftrightarrow g \in O(f)$ means that the function $g$ is growing at the same 
rate or slower than $f$. 

$$
O(f)  := \{g: \mathbb{N} \to \mathbb{R}^+ \mid \exists C > 0 \; \forall n \in \mathbb{N} \text{ such that } g(n) \leq C \cdot f(n)\}
$$

The $\Theta$-notation is used to compare two functions that **grow at a same rate**. For example $g = \Theta(f) 
\Leftrightarrow g \in \Theta(f)$ means that $g$ and $f$ grow at the same rate. Computing the limit of the division of
those two function results in a constant non-zero factor. 

$$
\Theta(f) := \{g: \mathbb{N} \to \mathbb{R}^+ \mid g \leq O(f) \text{ and } f \leq O(g)\}
$$

The $\Omega$-notation tells us that one function **grows at least as fast** as some other function. For example, $g \geq 
\Omega(f) \Leftrightarrow g \in \Omega(f)$ means that $g$ grows at least as fast as $f$. This is the case if 
$\lim_{n \to \infty} \frac{g(n)}{f(n)} = \infty$. 

$$
\Omega(f) := \{g: \mathbb{N} \to \mathbb{R}^+ \mid f \leq O(g)\}
$$

We can restate the above definitions using the limits: 

\begin{align}
\lim_{n \to \infty} \frac{f(n)}{g(n)} &= 0 &\text{, then } f \leq O(g)\\
\lim_{n \to \infty} \frac{f(n)}{g(n)} &= C \in \mathbb{R}^+ &\text{, then } f = \Theta(g)\\
\lim_{n \to \infty} \frac{f(n)}{g(n)} &= \infty &\text{, then } f \geq \Omega(g)
\end{align}

### Counting function calls

One thing that comes up often is the counting of some function call in pseudocode. The exercises often state to compute
the asymptotic bound, but sometimes also requires the exact number of calls. 

Here a couple of things to remember. 

- A loop (almost) always results in a formula, while it sums up the number of function calls in this loop.
- If an inner loop depends on the counting index of the outer loop, the gaussian sum is used: $\sum_{k=1}^n k =
  \frac{n(n+1)}{2}$, $\sum_{k=1}^n k^2 = \frac{n(n+1)(2n+1)}{6}$, $\sum_{k=1}^n k^3 = \frac{n^2(n+1)^2}{4}$.
- Loops that follow each other are added together.

??? tip "Examples"

    The following function has a complexity of $\Theta(n)$ regarding the function calls $f$ with an exact number of calls 
    of: 
    
    $$
    \sum_{i=0}^n 2 + \sum_{j=0}^{2n} 1 = 2(n+1) + (2n) + 1 = 4n+3
    $$
    
    ```
    i = 0
    while i <= n:
        f()
        f()
        i += 1
    
    j = 0
    while j \leq 2n:
        f()
        j += 1
    ```

    The following function has a complexity of $\Theta(n^4)$ regarding the function calls $f$ with an exact number of calls
    of:
    
    $$
    \sum_{i=1}^n \sum_{j=1}^{i^3} 1 = \sum_{i=1}^n i^3 = \frac{n^2(n+1)^2}{4}
    $$
    
    ```
    i = 1
    while i <= n:
        j = 1
        while j \leq i^3:
            f()
            j += 1
        i += 1
    ```


### Running-time of divide-and-conquer

To compute the running-time of divide-and-conquer algorithms, we use the following theorem. Let $a,C > 0$ and $b \geq 0$
be constant, and $T: \mathbb{N} \to \mathbb{R}^+$ for all even $n \in \mathbb{N}$.

$$
T(n) \leq aT(n/2)+Cn^b
$$

For all $n = 2^k, k \in \mathbb{N}$:

- If $b > log_2(a)$: $T(n) \leq O(n^b)$
- If $b = log_2(a)$: $T(n) \leq O(n^{log_2(a)} \cdot log(n))$
- If $b < log_2(a)$: $T(n) \leq O(n^{log_2(a)})$

The theorem supposes that the problem size is halved in each iteration. The value of $a$ signifies, how many recursive
calls are to be found in the function. $b$ is used to determine the complexity of the work that is done in each call
on the input elements. 
