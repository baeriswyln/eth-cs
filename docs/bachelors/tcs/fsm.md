# Finite-State machines

A finite-state machine (Endlicher Automat EA) is a state-machine capable of identifying words of a specific regular
language. Before getting started, we again need some notions.

An EA is defined as a five-tuple $M=(Q,\Sigma,q_0,F,\Delta)$.

- $Q$: finite set of states
- $\Sigma$: input alphabet (symbols that are processed by the EA)
- $q_0 \in Q$: starting state
- $F \subseteq Q$: set of accepting states
- $\delta: Q \times \Sigma \to Q$: the set of all possible state-transitions. The set essentially describes what state
  the machine will land in for every possible state combined with every possible symbol of the alphabet.
  $\delta(q_i,aw)$ outputs the state after the machine, which is currently in $q_i$, read the symbol $a$. The function
  $\hat{\delta}$ is quite similar but outputs the state after reading the entire word
- $Kl[q_i]$: class of the state $q_i$, describing the form of the words landing in said state

The following additional terms are important.

- **Configuration**: everything that is important for the future - $(q, aw) \in Q \times \Sigma^*$ denoting the current
  state and the remaining input.
- **Start-configuration**: $(q_0, w) \in \{q_0\} \times \Sigma^*$ denoting the starting state and the full input.
- **End-configuration**: every configuration $\in Q \times \{\lambda\}, the configuration after having read the full
  input.
- **Step**: expresses the relation $|_{\!\overline{\;M\;}}$ between two configuration such as
  $(q,w) |_{\!\overline{\;M\;}} (p,x)$ with $w \in \Sigma^*$, $w = ax$, $\delta(q,a)=p$.
- **Computation C**: describes a sequence $C = C_0,C_1,...,C_n$ of configurations such that $C_i |_{\!\overline{\;M\;}}
  C_{i+1}$. If $C_0$ is the start configuration and $C_n$ the end configuration, C is the computation of M on input x.
  $C$ can be accepting or rejecting, depending on the final state $q_n$. If $q_n \in F$, $C$ is accepting.

Now, let's see a couple of above definition using an example state machine. The following EA accepts the language
$L=\{w\in\Sigma_{bool}^* \big|\, |w|_0 \: mod\: 3 = 0\}$.

<figure markdown>
![](../../diagrams/b/tcs/ea_1.svg)
</figure>

$$
\begin{align*}
M &= \big(Q,\Sigma,q_0,F,\delta\big)\\
Q &= \{q_0,q_1,q_2\}\\
\Sigma &= \{0,1\}\\
q_0 &= q_0\\
F &= \{q_0\}
\end{align*}
$$

The following table describes the $\delta$ function.

| $\delta$ | 0     | 1     |
|----------|-------|-------|
| $q_0$    | $q_1$ | $q_0$ |
| $q_1$    | $q_2$ | $q_1$ |
| $q_2$    | $q_0$ | $q_2$ |

Now, assume we have input $x=01101$, the following computation is made.

$$
(q_0,01101) \; |_{\!\overline{\;M\;}} \; (q_1,1101) \; |_{\!\overline{\;M\;}} \; (q_1,101) \;
|_{\!\overline{\;M\;}} \; (q_1,01) \; |_{\!\overline{\;M\;}} \; (q_2,1) \; |_{\!\overline{\;M\;}} \; (q_2,\lambda)
$$

This is equivalent to $\hat{\delta}(q_0,x)=q_2$

The classes of above EA can be written as one generalized description with $i\in\{0,1,2\}$:

$$
Kl[q_i] = \big\{w \in \Sigma^* \big| \, |w|_0 \; mod \; 3 = i \big\}
$$

## Modularity

Big state machines for more complicated languages can be expressed as a cartesian product of multiple smaller
state-machines.

## Proof of non-existence of a shorter machine

To prove the non-existence of a smaller machine, we first start by constructing a possible suspected shortest
machine. For each state, we then provide a word with which the machine will land

## Proof of non-regularity

### Lemma 3.3

### Pumping Lemma

### Kolmogorov-Complexity

## Non-deterministic finite-state machine