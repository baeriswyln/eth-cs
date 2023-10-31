# Languages

Before starting to learn about alphabets, words and languages, we require a couple of definitions.

- **Alphabet** $\Sigma$: Non-empty set containing symbols, letters, numbers, etc.
    + $| \Sigma |$: Cardinality, number of elements in alphabet
    + $\mathcal{P}(\Sigma)$: Every possible combination of the elements. Note that the empty set must be counted as well
- **Word** $w$: finite string of symbols from $\Sigma$
    + $\lambda$: empty word, has length 0
    + $|w|$: length of word $w$
    + $|w|_a$: number of symbol $a$ in word $w$
    + $\Sigma^*$: set of all words that can be created using the alphabet $\Sigma$
- **Subword** $v$: part of a word. $\exists x,y \in \Sigma^* : w = xvy$ where $x$ is a prefix and $y$ a suffix to $v$
- **Language** $L$: set of words from $\Sigma^*$ with $L \subseteq \Sigma^*$
    + $L^c$: complement, all words but the ones from $L$. $L^c=\Sigma^* - L$
    + $L_\emptyset$: the empty language
    + $L_\lambda$: the language only containing the empty word
    + $L_1 \cdot L_2$: multiplication of two languages. Multiplying two words means concatenating them. Here, every word
      from $L_1$ is combined with those from $L_2$. Example: $\{a,ab\}\cdot\{\lambda,bc\}=\{a,abc,ab,abbc\}$
    + $|L|$: cardinality, number of words in $L$. Note that $|L_1\cdot L_2| \leq k \cdot m$ with $k=|L_1|,m=|L_2|$
    + $L^n$: exponentiation, multiplication of $L$ with itself $n$-times. $L^{n+1}=L^n\cdot L$. Two additional special
      cases: $L^* = \bigcup_{i\in\mathbb{N}}L^i$ and $L^+ = \bigcup_{i\in\mathbb{N}-\{0\}}L^i$

## Language properties

The following chapters discuss some properties and operations for languages and words.

### Concatenation

Words can be concatenated. $\Sigma^* \cdot \Sigma^* = \Sigma^*$. A concrete example is $abb\cdot bbc = abbbbc$. This
operation is **associative** ($(a\cdot b) \cdot c = a \cdot (b \cdot c)$ )  but not **commutative** ($a \cdot b \neq b
\cdot a$).

$a^R$ is the word $a$ but its letters are in reverse order; $a=a_1a_2a_3, a^R=a_3a_2a_1$.

### Cardinal order

To get the cardinal order of a language, its words are first sorted by length. Words of the same length are then sorted
lexicographically. In mathematical terms, the word $u$ is in front of $v$ if:

$$
|u| < |v| \lor (|u|=|v| \land u=xs_iu'\land v=xs_jv'\land s_i<s_j)
$$

The last two equalities and the last inequality look for the first letter that is different.

### Arithmetics with languages

Set operations can be applied on languages such as the subtraction, union, intersection, etc. The same rules apply as
to sets, eg.

$$
L_1L_2\cup L_1L_3 = L_1(L_2\cdot L_3)
$$

An additional definition defines the **homomorphism** between two languages $\Sigma_1^*,\Sigma_2^*$ as every function
$h: \Sigma_1^* \to \Sigma_2^*$ with the following properties: 

1. $h(\lambda)=\lambda$
2. $h(uv) = h(u) \cdot h(v)$ for all $u,v\in \Sigma_2^*$.

## Kolmogorov-Complexity

## Random objects

## Primefactor