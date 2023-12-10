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

The Kolmogorov-Complexity $K$ is defined as the **length of the shortest (Pascal)-program which is able to generate a
binary word $x$**.

The $K$ of the language $L_1 = \{0^n\}^\infty_{n=1}$ for example is defined as $K(0^n) \leq \lceil log_2(n+1) \rceil +
1$ where $c$
is the constant part of the program that is not linked to $n$, and $\lceil log_2(n+1) \rceil$ the size of the binary
representation of $n$.

Let's compare two possible programs for the language $L_2 = \{0^{n^3}\}^\infty_{n=1}$ and their resulting complexity.

=== "A"

    First we define the program **A** in pseudocode. 
    
    ```
    begin
      for I = 1 to n^3: write(0)
     end
    ```

    In above code, the value of $n^3$ is directly used as an input to the program. Consequently, the Kolmogorov-Complexity
    is computed as $K(A) = \lceil log(n^3+1) \rceil + c$. 

=== "B"

    Our second program **B** takes advantage of the fact that we do not care about the memory usage of our program. 
  
    ```
    begin
      J := n
      J := J * J * J
      for I = 1 to J: write(0)
    end
    ```

    The program above has as an input the value $n$. This is better than seen in program **A**. This fact is also being
    reflected in the Kolmogorov-Complexity $K(B) = \lceil log(n+1) \rceil + d$.

### Non-compressible words

Not all words can be compressed regarding the Kolmogorov-Complexity - this means that for all $n \in \mathbb{N}-\{0\}$
there exists a word $w_n \in (\Sigma_{bool})^n$ such that $K(w_n) \geq |w_n| = n$. In other words, a word of length $n$
exists for every $n$ that is non-compressible.

The language $\{0,1\}^n$ containing only words of length $n$ for example has at least two words that are not
compressible. The language contains $2^n$ words, but there exist not enough distinct non-empty programs with length
strictly smaller than $n$. To be precise, only $2^n-2$ such programs exist.

For the language $\{0,1\}^{\leq n}$ this gets even worse as now $n^{2+1}-2$ distinct words exist but there are still only
$2^n-2$ distinct programs. Thus, at least half of the words have $K(x) \geq |x|$.

Such words are called **random**. The same can be said for numbers. A number $n$ is called random if
$K(n) = K(Bin(n)) \geq \lceil log_2(n) \rceil -1$.