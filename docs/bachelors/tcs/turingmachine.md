# Turing-machine

The turing-machine (TM) is a concept of a machine that can detect (almost) all languages. It is based on an infinite
work tape (which can be seen as an infinite amount of storage) and a finite control (or unformally a "program"). The
following diagram describes a turing-machine with all its basic components.

<figure markdown>
![](../../diagrams/b/tcs/tm_1.svg)
</figure>

The formal definition of a TM is a seven-tuple $M=(Q, \Sigma, \Gamma, \delta, q_0, q_{accept}, q_{reject}) with the
following symbol definitions.

- $Q$: finite state set
- $\Sigma$: input alphabet
- $\Gamma$: working alphabet, $\Sigma \in \Gamma, (¢, \_\_) \in \Gamma - \Sigma, \Gamma \cap Q = \emptyset$
- $\delta$: $Q- \{q_{acc}, q_{rej}\} \times \Gamma \to Q \times \Gamma \times \{L,R,N\}$ where $L,R,N$ are the movements
  that the R/W-head is able to do (move left 1, move right 1, neutral)
- $q_0$: start state
- $q_{accept}$: accepting state, also $q_{acc}$
- $q_{reject}$: rejecting state, also $q_{rej}$

A **configuration** is an element from $Konf(M)=\{¢\}\cdot \Gamma^* \cdot Q \cdot \Gamma^+ \cup Q\{¢\}\Gamma^+$.
$\Gamma^*$ describes the tape content to the left of the R/W-head, $Q$ the current head position, and $\Gamma^+$
everything to the right of the head. The second element of the union describes the special case when the head is on
the end mark (where the head should not be able to go further to the left).

The **start configuration** for some input $x$ is $q_0¢x = Q_0$. Further configurations can then be denoted as
$¢q_1qaw_2$, where $q$ is the head position, and $a$ is the symbol at said position.

Let's consider a **step** where the head is currently on $x_i$and will replace said symbol with $y$. There are three
possible steps to be taken. Here, we transition from one config $Q_j$ to the next configuration $Q_{j+1}$:

- No movement: $₵x_1x_2...x_{i-i}qx_ix_{i+1} \; |_{\!\overline{\;M\;}} \; ₵x_1x_2...x_{i-i}qyx_{i+1}$ which is identical
  to $\delta(q,x_i)=(p,y,N)$
- Movement left: $₵x_1x_2...x_{i-i}qx_ix_{i+1} \; |_{\!\overline{\;M\;}} \; ₵x_1x_2...x_{i-i}qyx_{i+1}$ which is
  identical to $\delta(q,x_i)=(p,y,L)$
- Movement right:$₵x_1x_2...x_{i-i}qx_ix_{i+1} \; |_{\!\overline{\;M\;}} \; ₵x_1x_2...x_{i-i}qyx_{i+1}$ which is
  identical to $\delta(q,x_i)=(p,y,R)$

A **computation** is a sequence of configurations $Q_1,Q_2,...$ with $Q_i \; |_{\!\overline{\;M\;}} \; Q_{i+1}$. A TM
can **run indefinitely**, or **stop** on $w_1qw_2$ with $q in \{q_{acc},q_{rej}\}. The TM can then be **accepting** if 
it stops on the accepted state, or **rejecting** if it stops on a rejecting state or if it is running indefinitely.