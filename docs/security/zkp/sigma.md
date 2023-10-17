# Sigma protocols

The sigma protocols are the shortest and nicest possible interactive proofs. It is a 3-move public-coin protocol with
the following properties:

- V always accepts if $(x,w)\in R$
- k-special sound, which means that an extractor exists that can extract the witness.
- SHVZK

## Commitment schemes

Sigma protocols require commitment schemes. A real world example of a commitment scheme are envelopes: a message is
put into a (cryptographic) envelope, that can then no longer be opened by some other entity. The sender committed to a
certain message and nobody is able to change this without the others noticing it.

The commitment scheme is a collection of three algorithms:

- **$Setup(1^\delta$)**: public parameters **pp** are generated. Message space $\mathfrak{M}$, randomness space
  $\mathfrak{R}$, decommitment space $\mathfrak{D}$ and commitment space $\mathfrak{C}$.
- **$Commit(pp,m\in\mathfrak{M}, r \gets_\$ \mathfrak{R})$**: creates a commitment and a decommitment $(c,d)$.
- **$Verify(pp,c\in\mathfrak{C},d\in\mathfrak{D},m\in\mathfrak{M})$**: asks question if message was in commitment, and
  consequently outputs 0 or 1.

The commitments must be correct and secure. From this, the following points follow.

- **Correctness**: $\forall pp, Verify(pp,Commit(pp,m,r),m)=1$
- **Perfect hiding**: the commitment should not disclose anything about the message.
- **perfect binding**: it must not be possible to change the message.

Hiding and binding also exist as **computational hiding** and **binding**. It is not possible to have **perfect binding
** and **perfect hiding** at the same time.

### Elgamal encryption

The elgamal is a computationally hiding and perfectly binding example of a commitment scheme. We sample a random prime
number h (prime $p$ of order $2^\lambda$ from $\mathbb{G}$) and a secret key $s$, which can then be used to compute
$g = s \cdot h$. $\mathbb{Z}_p$ is the collection of integers from 0 to p-1, but the operations inside this set are done
with **mod p**.

- **Setup** outputs $pp := (\mathbb{G}, g, h, p)$
- $Commit(pp,m\in\mathbb{G},r\gets_\$\mathbb{Z}_p)$: $((c_1,c_2),d):=((m+r\cdot g,r \cdot h),r)$
- $Verify(pp,c,d,m)$: $(Commit(pp,m,d)==c)$

### Pedersen commitment

The pedersen commitment can be plugged into sigma protocols to have a _perfect hiding_ and _computationally binding_
proof, and has the following steps. The protocol is perfectly hiding because $r\cdot h$ is uniformly random in
$\mathbb{G}$.

- **Setup** generatesagain the same output.
- $Commit(pp,m\in\mathbb{Z}_p,r\gets_\$\mathbb{Z}_p)$: $((c_1,c_2),d):=((m\cdot g,r \cdot h),r)$
- $Verify(pp,c,d,m)$: $(Commit(pp,m,d)==c)$

## Example - three-coloring graph

In a three-coloring graph, all adjacent nodes must be of different colours. The IP is as follows ($E$ is the set of
edges):

- Prover creates a permutation of the graph (the colors change, but the three-coloring stays valid) $\sigma$.
  Additionally, the set of colors $col$ is extracted. A commitment is created: $(c_u,d_u) \gets_\$ Commit(pp,col_u)$.
- Prover only sends the commitment $c_u$ to the verifier ($u$ is a vertex).
- Verifier picks a random edge $(a,b)$ from the graph and sends that to the prover
- Prover sends colors and decommitments of the requested edge to the verifier
- The verifier checks the commitment and decommitment.

**Completeness**: the three-coloring stays valid after the permutation. Additionally, the commitment scheme makes sure
that the verifier is always capable of correctly verifying the commitments/decommitments.

**$|E|$-special soundness**: the tree of transcript must contain all edges, and no adjacent nodes must be the same
color.

There exists a simulator for _SHVZK_ which reproduces the verifier's view that is the following (for a single edge in
this example):$((c_u)_{u\in V}(a,b),col_a,d_a,col_b,d_b)$, and $Verify()==1$ for both nodes a and b, and $col_a \neq
col_b$. The simulator $S(pp,G,(a,b))$ does the following steps (note that the simulator does not know the graph). Note
that the simulator is kind of going backwards in the protocol.

1. Sample two random colors $col_a, col_b$ with $col_a \neq col_b$
2. $(c_a,d_a) \gets_\$ Commit(pp,col_a)$
3. $(c_b,d_b) \gets_\$ Commit(pp,col_b)$
4. All other nodes can be committed to some color (does not matter what, as they stay hidden)
5. Results in the verifier's view

## Composition

Often, protocols are repeated to improve the completeness/soundness errors. This might happen in **sequence repetition**
or **parallel repetition**. The $\Sigma$-protocols are often used with parallel repetition. The protocol stays largely
unchanged, but instead of sending a single message, multiple messages are sent at once from the prover to the verifier. 

A 2-sound $\Sigma$-protocol for example sends two messages in parallel, which will reduce the soundness error from
$\frac{1}{|C|}$ to $\frac{1}{|C|}$.