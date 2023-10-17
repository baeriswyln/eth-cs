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
prover and verifiers. Each message $a_i$ contains all $i-1$ previous messages, as well as the input $x \in \{0,1\}^*$
and
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

## Zero-knowledge (ZK)

The following chapters can be summarised with the following points:

- Perfect soundness and perfect zero knowledge cannot be achieved at the same time.
- Knowledge-soundness also good for "trivial" languages
- Special soundness and SHVZK are those that are used the most.

|                                       | Soundness                                 | Zero Knowledge      | Proof size      |
|---------------------------------------|-------------------------------------------|---------------------|-----------------|
| Proofs   (short term secrecy)         | Perfect/statistical                       | Computational       | Not compressing |
| Arguments (short window for cheating) | Computational (short window for cheating) | Perfect/statistical | <<\|x\|, \|w\|  |

### Perfect Zero-knowledge

Perfect zero-knowledge is a special case of zero-knowledge proofs, and only a few of those exist. The proof must make
sure that the verifier gained no new knowledge - essentially, the verifier should be able to reproduce the proof by
themselves. $(P,V)$ is a perfect zero-knowledge proof, in case an efficient simulator $S$ exists that is able to
reproduce the verifier's view $(x,s,a_1,...,a_{k(x)})$.

The following example describes a perfect ZKP for graph isomorphism. We have two graphs $G_0, G_1$ that are isomorphic (
there exists a permutation mapping $G_0$ to $G_1$).

1. **Prover** takes a random permutation $\sigma$, and permutes $G_0$ using this permutation which produces the graph
   $H$. $H$ is sent to the verifier.
2. **Verifier** sends randomly either 0 or 1 to the prover.
3. **Prover**: if a 0 was received, prover sends $\sigma$ from step 1 to the verifier. If 1 was received,
   $\sigma \circ \pi$ is sent to the verifier (with $G_0 = \pi(G_1)$).
4. **Verifier** now applies the permutation sent by the prover either to $G_0$ or $G_1$ (depending on the random value
   sent in step 2.). If the graphs are isomorphic, both ways should result in the random permutation $H$.

- **Completeness** is good, as V will always accept if the graphs are isomorphic and the prover knows the permutation.
- **Soundness**: say there graphs are not isomorphic, there exists no $H$ that is both isomorphic to $G_0$ and $G_1$ at
  the same time. This means that the generated $H$ is only isomorphic to one of the graphs, and in the conversation, V
  will only accept the provers response with a probability of 50%.

This perfect zero-knowledge can now be proven using a **simulator**. Such a simulator is capable of reproducing the
**verifier's view**, which in this case is $View_{V^*}^P = (G_0,G_1,\tau,H,b)$, with $V^*$ a malicious verifier. The
simulator $S(V^*,G_0,G_1)$ then goes through the following steps:

1. $\tau$ a random distribution
2. $B$ a random sample of 0 or 1
3. $H = \tau(G_B)$
4. Sample $b$ (just as malicious verifier would)
5. If $b \neq B$, start from begining
6. Output the expected verifiers view.

### Variations of Zero-knowledge

As mentioned before, perfect zero-knowledge is only possible for some languages. Thus, some relaxations are designed.

- **Black-box** zero-knowledge: the simulator only gets black-box access to the malicious verifier, which means that
  he has no in-depth access to V, and is only capable of requesting a next symbol. It is noted as $S^{V^*}$.
- **Semi-honest verifier (SHVZK)**: uses an honest verifier that follows the protocol, but the secret is chosen very
  carefully. For this reason, the simulator requires access to said secret.
- **Honest verifier**: the simulator has access to an honest verifier, which makes the simulator's job easier. Verifier
  is following the protocol.
- **Statistical ZK**: the simulator can simulate the malicious verifier if he gets lucky.
- **Computational ZK**: the simulator can simulate the malicious verifier if he gets lucky or is hardworking.
- **ZK with auxiliary inputs**: additional input is given to prover and verifier (e.g. hint, witness, conversations of
  prior protocol). The simulator requires additional input as well.

### Variations of Soundness

- **Knowledge soundness**: $\kappa$ is the error of how sure one is that the prover is sound. An extractor $E$ extracts
  a witness from the prover (somewhat similar to black-box ZK, but the extractor has oracle access to the malicious
  prover, producing a witness). The extractor can get both transformations $\tau$ from the prover (by inputting 0 and
  1). The extractor can then extract $\pi = \tau_1 \circ \tau_0^{-1}$
- **Interactive arguments**: a proof should be convincing. But in this case, arguments can be held between the two
  parties, because the protocol only holds against __efficient provers__. This means that cryptographic assumptions can
  prove the soundness.
- **Special soundness**: connected to the *public coin protocol*. A *knowledge extractor* is used, taking the *tree of
  transcripts* as an input to extract a witness. If such an extractor exists, the protocol with (2k+1) moves is
  $(n_1,...,n_k)-special sound$. This soundness is typically easier to compute.
- **Perfect**, **statistical** and **computational soundness** also exist.

### Public coin protocols

In general, the verifier publicly sends random "coin flips" to the prover. Those are the only messages that are sent by
the verifier, and are often also called **challenges**.

The public coin flips can then be arranged in a **tree of transcripts**. The number of challenges defines the depth of
the tree.