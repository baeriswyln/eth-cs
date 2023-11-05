# Computability

Computability is one of the first theories described in computer science. It is used to classify problems into 
algorithmically solvable and non-solvable problems. 

## Infinities

At first, we must realize that there are different types of infinity. For example, there are more languages than 
there are turing-machines: $|KodTM| < |Pot(\Sigma^*)|. We know however, that an infinite amount of TMs and an infinite
amount of words in the power product exist. Let's start with the following definition. We have the sets $A$ and $B$.

- $|A| \leq |B|$, if an injective function $f: A \to B$ exists.
- $|A| = |B|$, if $|A| \leq |B|$ and $|B| \leq |A|$ (thus a bijective function exists).
- $|A| < |B|$, if $|A| \leq |B|$ and no injective function $f: B \to A$ exists.

We can make a first observation with the two sets $|\mathbb{N}| = |\mathbb{N}_{even}|$. One would think that the first
set would be bigger, as the second only contains every second number. However, by the above definitions, we find that
they are equivalent as the bijective function $f(i)=2i$ exists. 

A set is defined as **countable** if $|A|$ is finite, or if $|A| = |\mathbb{N}|$.

### Hilbert Hotel

The hilbert hotel is an intuition to compare finite sets. We assume an infinite hotel where the rooms are enumerated 
as $1,2,...$. Now, a new guest arrives and we want to find a room for this guest. We tell all the current guests that 
they should move into the room next to theirs ($i \to i+1$). Room 1 gets empty, and the new guest can move into this 
room. 

Now, if a bus with an infinite amount of guests arrives, we can now tell all the guests in to the room with double 
the room number of their current room ($i \to 2i$). 

### Examples of infinities

We have seen  that $\mathbb{N}$ is countable. We can also show that $(\mathbb{N} - \{0\}) \times (\mathbb{N} - \{0\})$
is countable as well. We do this by setting up a matrix where the rows and the columns contain the two sets that are
multiplied. We then define a canonical order according to the red arrows. With this order, we can enumerate all values
present in said set. 

<figure markdown>
![](../../diagrams/b/tcs/countability_1.svg)
</figure>

The same way we can also show that $\mathbb{Q}$ is countable. The rows contain the enumerators, and the columns the
dividers. 

### Diagonalisation

A set that is not countable is the real numbers in the interval $[0,1] = \{x \in \mathbb{R} \mid 0 \leq x \leq x\}$. To
prove this, we first define the following table where we enumerate real numbers in said interval. 

<figure markdown>
![](../../diagrams/b/tcs/countability_2.svg)
</figure>

We define a new entry to the set $C$ as $C=0.C_1C_2C_3...$ with $C_i \neq a_{ii}, c_i \notin \{0,9\}$ for all $i$. 
The new entry, however, is not part of the table as it differs on the diagonal $a_{ii}$ from all other entries (as 
we defined that $C_i \neq a_{ii}$).

Similarly, we define a new table that combines $w_i \in \Sigma^*_{bool}$ and $M \in KodTM$. $d_ij = 1$ if $M_i$ 
accepts the word $w_j$.   

<figure markdown>
![](../../diagrams/b/tcs/countability_3.svg)
</figure>

We now construct the diagonal language denoted as $L_{diag}$ is not in $\mathcal{L}_{RE}$ (the set of recursive 
languages). The constructed language does not yet exist in the set $L(M_i)$.

$$
L_{diag} = \{w \in (\Sigma_{bool})^* \mid w = w_i \; for \; some \; i \in \mathbb{N} - \{0\} \; and \; d_{ii} = 0\}
$$

## Reduction

## Universal language

## The "Post'sche Korrespondenzproblem" (PKP)