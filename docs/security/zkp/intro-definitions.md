# Introduction and definitions

## Proofs

In general, a proof is used to establish trust between two parties, where a statement is established to be valid.
Such a statement is established between two parties:

- **Prover P** (proof writer): Wants to prove a statement
- **Verifier V** (proof checker): verifies that the proof is valid

Those parties are interacting with each other in different ways. See the following proof types:

- **Mathematical proof**: One-directional proof, where the _prover_ makes a sequence of logical assertions. All errors
  made are detectable by the _verifier_.
- **Interactive proof (IP)**: Bi-directional proof, where the messages are randomised and adaptive. **Most** errors are
  detectable by V. Increase proving power (more complex problems can be proven), and increased efficiency.

Now, a general (dirty) description of Zero-Knowledge Proofs could be: Mathematical proofs that were extended
with interactions, randomness, and cryptographic assumptions.

## Complexity classes

Problems can be separated in different complexity classes. Each class determines how problems can be solved. This mostly
concerns the runtime of solving the problem. A Complexity class is a set of **languages** (problem types with similar
properties, e.g. time complexity). A language $L$ is a **collection of problems** $L \subseteq \{0,1\}^*$, where each
**problem statement** $x$ is a binary encoding of YES/NO $x \in \{0,1\}^*$.

- **P**: Problems are solvable in polynomial time (efficient) and are thus easy to solve. There exists an algorithm $M$
  that is able to say if $x \in L$ or $x \notin L$. This is obvious and needs no proof.
- **NP**: Problems where it is easy to say that $x \in L$ using a **witness w**: $(x,w) \in R_L$ However, there are no
  checks in polynomial time that can prove that $x \notin L$. $x \in L$ has proof which is easy to check. However, the
  witness might be hard to find.
- **IP** (interactive proof): Problems are proven using a prover and a verifier. They exchange a certain amount of
  messages and the verifier concludes if the proof is valid or not. The verifier must be running polynomial time.
  The algorithms are able to verify the two following properties:
    - **Completeness** (usually accepts true statements): $\forall x \in L, Pr_{r,s}[\langle P(r),V(s)\rangle (x) = 1]
      \geq \frac{3}{4}$
    - **Soundness** (usually rejects false statements): $\forall x \notin L, \forall P^*, Pr_
      {r,s}[\langle P^*(r),V(s)\rangle (x) = 1] \leq \frac{1}{2}$

In above formulas, $Pr_{r,s}$ denotes a proof system that uses the random values $r$ and $s$, and $P^*$ is any malicious
prover. The probability to accept/reject the statements can be changed, as long as acceptance is greater than rejection
probability.

Increased proving power (seen in the previous chapter) allows for proofs of larger complexity classes.

## Interactive Algorithms and Interactive Proof

Interactive algorithms are used to perform **interactive proofs**. A series of $k$ messages are exchanged between the
prover and verifiers. Each message $a_i$ contains all $i-1$ previous messages, as well as the input $x \in \{0,1\}^*$ and
a random value $r$ or $s$. The complete interaction is denoted as $\langle P^*(r),V(s)\rangle (x) = a_{k(x)}$. All 
messages are stored in the **transcript** $(x,a_1,...,a_{k(x)})$ with $a_{k(x)}$ being the verifiers conclusion if he 
accepts or rejects the proof. 

$$
\begin{align}
a_1 &= P(x,r) \\
a_2 &= V(x,a_1,r) \\
a_3 &= P(x,a_1,a_2,r) \\
\vdots \\
a_{k(x)} &= V(x,a_1,...,a_{k(x)-1},s)
\end{align}
$$

### Graph Isomorphism

A problem that was seen in the course as an example is the graph-isomorphism problem. To our knowledge, no algorithm 
exists that can proof if a graph is isomorphic. **Isomorphic graphs** are graphs that be mapped between each other using 
some permutation $\pi$. 

## Zero-knowledge

### Perfect Zero-knowledge

Perfect zero-knowledge is a special case of zero-knowledge proofs, and only a few of those exist. The proof must make 
sure that the verifier gained no new knowledge - essentially, the verifier should be able to reproduce the proof by 
themselves. $(P,V)$ is a perfect zero-knowledge proof, in case an efficient simulator $S$ exists that is able to
reproduce the verifier's view $(x,s,a_1,...,a_{k(x)})$.