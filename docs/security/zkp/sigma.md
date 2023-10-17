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

- **$Setup(1^\delta)$**: public parameters **pp** are generated. Message space $\mathfrak{M}$, randomness space
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

### AND composition

The sigma protocol is capable of combining two relations $R$ and $R'$ into a single relation $R_\land$. The messages
exchanged are similar to the parallel repetition.

For this composition to work, both $R$ and $R'$ require that the verifier's challenge space $\mathfrak{C} is identical.

1. **P** then sends first messages using both sigma protocols for $R$ and $R'$
2. **V** sends a single challenge $c$. This is valid because the relations must share the challenge space
3. **P** sends back a message according to the challenge
4. **V** must then accept both individual verifiers

The AND composition of two sigma protocol is pretty straight forward. The simulator for $R_\land$ is the combination for
the individual relations: $S^\land(x,x',c) := (S(x,c),S'(x',c))$.

The protocol is complete, k-special-sound and SHVZK.

### OR composition

The preconditions for this composition is the same as for the AND composition. There is, however, a difference in how
the prover and verifier work together. To start, the prover has only a witness for one of the relations. The prover for
the second relation is simulated using a simulator.

1. **P** generates message $a$ using prover $P_1$
2. **P** generates a random $c'$ from the challenge space
3. **P** simulates the message $a'$ and $z'$ using $c'$
4. **P** sends both $a$ and $a'$ to the verifier
5. **V** responds with a new random $c''$
6. **P** computes $c := c''-c'$, which is then used to compute $z$
7. **P** sends $z,z'$ and $c,c'$ to the verifier
8. **V** checks that both challenges were correctly answered, and $c+c' == c''$

The protocol is complete, k-special-sound and SHVZK.

## Multi-Party computation

In this protocol $\Pi$, multiple players $P_i$ want to arrive at a common conclusion (joint function) without leaking
any of their private information $w_i$, which also function as the private inputs. The players interact with each other
over multiple rounds, and in the end all players should get the output. Additional inputs are a public input $x$, and
private randomness $r_i$.

The protocol is then specified by a **next message function**, specifying what message $m$ a player $i$ will send in
round $j$ to some other player $k, k\neq i$. The output is the message that is going to be sent by player $P_i$ in round
$j+1$. $M$ are all messages that were received by a certain player. 

$$
\Pi \big(i,x,w_i,r_i,(M_1,...,M_j)\big) \to (m_{j+1,i\to k})
$$

The protocol is correct, if all players get the correct output. If one input is not in the relation, all players 
will get 0. 

The protocol has also **t-privacy**. This means that up to $t$ players can be corrupted until the protocol looses its
privacy. 

### View

A player's **view** is defined as $View_i =(x,w_i,r_i,M_1,...,M_k)$ where $M_j$ is the set of all messages player $P_i$
received from all other players in a specific round $j$.

Two views $View_i, View_j$ are called **consistent**, if the messages in $View_i$ sent by $View_j$ actually match the 
messages that can be computed using $View_j$ and $\Pi$. This is called _local consistency_

To get global consistency, all combinations of views must be pairwise consistent. If this is the case, there exists a 
protocol with input $x$ that matches all views.  

### Sigma-Protocol with MPC in the head

This protocol is called 'in the head' because the prover imagines the graph of players and computes all their witness
and their views by themselfs. We now have $P(pp,x,w)$ and $V(pp,x$) with $pp$ the parameters from the setup function.

1. **P** computes all witness for all players $w_i$, with the additional condition that $w = w_1 \oplus ... \oplus w_n$
2. **P** uses all $w_i$, input $x$ and $\Pi$ to compute the views
3. **P** commits to the views, and sends all commitments to the verifier
4. **V** chooses random players $i,j$
5. **P** sends the views and decommitments of $i,j$ to **V**
6. **V** runs the verifying function Verfy$(pp,c_i,d_i,View_i)=1$, makes sure the views are consistent, and that 
   $P_i = P_j = 1$

## Fiat-Shamir Heuristic

The protocol gives full zero knowledge and reduces the number of messages exchanged to 1. Additionally, a hash function
$H$ is required, that is modelled as a random oracle. The hash function maps onto the challenge space $C$. Additionally, 
$H$ remembers the outputs (uniform random hash) that it has given for previous inputs. Giving the same input again will
result in the identical output. 

Unlike the sigma protocol seen until now, **P** is generating $c = H(x,a)$. He then sends $a$, $c$, and $z$ to the 
verifier. **V** does the same checks as usual, but also makes sure that $c$ was generated correctly using the common
hash function.

Some notes: 

- Hash more than less. If a commitment scheme is involved for example, the public parameters pp should be hashed as well
- Unsecure for real hash functions (only certain hash-functions can be used for a secure protocol)
- Signature scheme can be implemented, where the prover signs, and the verifier verifies. This method was used in the
  _picnic_ signature scheme that was a candidate as a post-quantum signature scheme. 

## Sigma protocol against malicious verifiers

If the verifier generates random challenges, there is no way to gain knowledge. However, non-uniformly-random challenges
might leak information.

To counter this, a public coin flip protocol is used. The verifier is forced to be honest by generating challenges 
together with the prover.

The prover generates his challenge $c$ and commits to it. The commitment $C$ is then sent to the verifier. V also 
generates a secret $c'$ that he sends back to the prover. P then answers with $c$ and the decommitment $d$, allowing 
the verifier to check that $c$ was not changed in the meantime. This procedure can then be interleaved with a message:

1. **P** computes $a$, $c$, and $C$, sending $a$ and $C$ to **V**
2. **V** generates $c'$ and sends it to **P**
3. **P** computes $z \gets_\$ P_2(x,w,a,c'';p)$ with $c'' := c + c'$, then sends $z,c,d$ to **V**
4. **V** verifies that $c$ has not been changed, and computes the output $V(x,a,c'',z)$

