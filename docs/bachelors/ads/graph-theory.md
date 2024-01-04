# Graph theory

A graph $G$ is a set of vertices $V$ and edges $E$ where an edge is defined as an _unordered pair_ of vertices $e = uv$
with $u \neq v$. Directed graphs are quite similar, but edges are defined as _ordered pairs_.

??? info "Variable names"

    The following chapters will use a series o variable names that, if not signaled otherwise, have the following 
    meaning.

    - $V$: set of vertices
    - $E$: set of edges
    - $e$: one specific edge
    - $n = |V|$: number of vertices in the graph
    - $m = |E|$: number of edges in the graph
    - $d(u, v)$: distance function between vertices $u$, $v$
    - $c(e)$: cost/weight of the edge $e$

## Definitions

**Vertices**:

- **Vertex degree $\text{deg}(u)$**: Number of adjacent vertices. In a directed graph, this is further split into
  $\text{deg}_{in}$ (number of incoming edges) and $\text{deg}_{out}$ (outgoing edges)
- **Source (Quelle)**: Vertex with $\text{deg}_{in} = 0$
- **Sink (Senke)**: Vertex with $\text{deg}_{out} = 0$ (exists, if the graph is acyclic)
- **Leaf**: Vertex with $\text{deg}(u) = 1$
- **Adjacent (Adjazent, benachbart)**: two vertices directly connected through an edge
- **Incident (Inzident, angliegen)**: edges connected to a vertex
- **$u$ reaches $v$**: There exists a path from $u$ to $v$
- **Strongly connected** (in directed graphs): both edges $uv$ and $vu$ exist
- **Loop**: an edge $uu$

**Graphs**:

- **Complete graph**: all vertices are pairwise adjacent
- **Transitive graph**: for any two edges $uv$ and $vw$, the edge $uw$ also exists
- **Connected component (Zusammenhangskomponente ZHK)**: biggest possible subgraph where there exists a walk between
  every pair of vertices.
- **Connected graph (zusammenhängend)**: Graph with exactly one connected component
- **Tree**: connected graph without cycles. A tree with $n$ vertices has $n-1$ edges
- **Multi-graph**: edges are allowed to be duplicated

**Walks**:

- **Walk (Weg)**: sequence of adjacent vertices with two endpoints where the length is defined as the number of
  vertices on the walk
- **Closed Walk (Zyklus)**: walk with identical first and last vertex
- **Path (Pfad)**: walk without repeating vertex
- **Cycle (Kreis)**: path with identical first and last vertex

**Special walks**:

- **Eulerian walk**: walk using every edge exactly once. Exists if at most two vertices have an odd degree.
- **Eulerian closed walk**: closed walk using every edge exactly once. Exists if all edges have an even degree, and
  only one ZHK exists.
- **Hamiltonian path**: path visiting every vertex exactly once. A graph with a Hamiltonian path also has an
  Eulerian walk.
- **Hamiltonian cycle**: cycle visiting every vertex exactly once.

### Finding Eulerian walk

We define the algorithm `Walk(u)` that finds a walk in a graph with starting vertex $u$ where each edge is used at
most once, and all edges incident to the final node were used.

```
Walk(u):
    if u has adjacent vertex v, and edge uv is not marked:
        mark uv
        Walk(v)
```

This algorithm has the **invariant**: number of unmarked edges incident to $v$ is even. If the invariant is valid
before the execution of $Walk(v)$, it is also valid afterward. Actually, if it is valid before the execution, the
algorithm will find a _closed walk_.

By slightly modifying this algorithm, we are able to use it to find an Eulerian walk. We put every vertex into a global
list `z` after it has been fully processed (has no more unmarked incident edges). The marking is also global.

```
EulerianWalk(u):
    for every adjacent vertex v of u, with uv not marked:
        mark uv
        EulerianWalk(v)
    z <- (z, u)
```

By executing the above algorithm on any node in the graph, we can find an Eulerian cycle in $z$ **IFF** the graph is
connected, and all vertex degrees are even. See the following example.

<figure markdown>
![](../../diagrams/b/ads/eulerian-walk.svg)
</figure>

### Storing a graph in memory

An **adjacency matrix** stores the edges in a square matrix of size $|V|^2$. Due to its size, it is often considered
inefficient, as most of the cells will be empty (set to $0$), except if $|E| \geq \Omega(|V|^2)$.

The **adjacency list** is a list of vertices, where for each vertex a linked list of nodes is stored. This linked
list represents all the adjacent vertices.

The improved **adjacency list** stores some additional data: for each vertex, we know the vertex degree, and
identical edges in the adjacency list are linked together.

## Topological order

The topological order is a special order of the vertices of a directed acyclic graph (DAG, a directed graph without
cycles). Such an order is not necessarily unique.

### Search algorithms

To find a topological order, we follow the steps below:

1. Find a sink $v$
2. $v$ is the last element in the topological order
3. Remove $v$ and call this algorithm recursively

To find a sink in the graph, we use the following algorithm on an arbitrary vertex. The last vertex in the path is a
sink.

```
Path(u):
    mark u
    if u has unmarked descendant:
        Path(u)
```

### Depth first search (DFS)

To find a complete topological order of a graph, we can use a so-called **depth first search**. The algorithm is
called on any vertex of the graph, giving us a **pre-** and **post-numper**. It is called depth first, because the
algorithm first explores a final vertex in the path and then goes up one level to continue searching.

```
DFS(G):
    for unmarked v:
        Visit(v)
        
Visit(u):
    pre[u] <- T;  T++
    for unmarked descendant v of u:
        Visit(v)
    post[u] <- T; T++
```

If this algorithm is called on the root of the topological order, this results in a DFS-tree, otherwise in a
DFS-forest. The reverse post-order results in the topological order (if we have a DAG).

In the following example, the algorithm is executed on a graph with cycles. In red, we have the pre- and post-number.

<figure markdown>
![](../../diagrams/b/ads/dfs-graph.png)
</figure>

The pre- and post-number of a vertex $u$ creates the interval $I_u$

The edges are colored as follows:

- Green: **tree edge** - edge building up the DFS-tree
- Yellow: **backward edge** - remaining edges $uv$ where $I_v$ completely contains $I_u$
- Blue: **forward edge** - remaining edges $uv$ where $I_u$ completely contains $I_v$
- Red: **cross edge**: - remaining edges $uv$ where $I_u$ is entirely in front of $I_v$

This allows us to build a **recursion tree/DFS-tree**:

<figure markdown>
![](../../diagrams/b/ads/dfs-tree.png)
</figure>

If a graph has a backward edge, it follows that the graph has a cycle. And thus no topological order exists.

DFS can also be executed on undirected graphs, with the following properties:

- forward edge = backward edge
- cross edges do not exist.

## Shortest path

In graphs, we are searching for shortest paths between two vertices. This must always be a path, as visiting a
vertex twice would unnecessarily add more edges to cross. Each edge is weighted, and these weights can also be
negative.

- Path cost (Wegkosten) $c(W)$: sum of the edge weights
- Distance $d(u,v) = \text{min}\{c(W) \mid \text{W is walk from } u \text{ to } v\}$
- $d(u,v) \leq d(u, w) + d(w,v)$

Once the shortest paths towards all vertices was found, the **shortest-path-tree** can be constructed, spanning up a
tree where a path from the starting vertex to all other vertices exists.

The following chapters will look at five algorithms that can be used in different use cases to find shortest paths.

=== "1-to-all"

    | Edge costs           | Algorith         | Running time          |
    |----------------------|------------------|-----------------------|
    | $c(e)=1$             | BFS              | $O(m+n)$              |
    | $c(e)\geq 0$         | Dijkstra         | $O((m+n) log(n))$[^1] |
    | $c(e) \in \mathbb{R}$ | Bellman-Ford[^2] | $O(m \cdot n)$       |

=== "all-to-all"

    | Edge costs           | Algorith                    | Running time                  |
    |----------------------|-----------------------------|-------------------------------|
    | $c(e)=1$             | $n \times$ BFS              | $O((m+n) \cdot n)$            |
    | $c(e)\geq 0$         | $n \times$ Dijkstra         | $O((m+n) log(n) \cdot n)$[^1] |
    | $c(e) \in \mathbb{R}$ | $n \times$ Bellman-Ford[^2] | $O(m \cdot n^2)$             |
    | $c(e) \in \mathbb{R}$ | Floyd-Warhsall              | $O(n^3)$                     |
    | $c(e) \in \mathbb{R}$ | Johnson                     | $O(n (m + n) log(n))$        |

[^1]: Using a priority queue (e.g. MinHeap)
[^2]: Requires the graph to have no negative cycles

### Breadth first search (BFS)

The breadth first search algorithm can be used to find the shortest path in an unweighted graph, where only the
number of crossed vertices are counted. It is called breadth first, as each vertex is fully processed, before going
a level further. This is achieved using a queue. Actually, this algorithm not only computes the distance between two
vertices, but between a starting vertex and all other connected vertices.

```
BFS(s):
    queue = {s}
    dist[s] = 0
    enter[s] = 0
    T = 1
    
    while !queue.empty:
        u = queue.deq()
        leave[u] = T; T++
        for (u,v) in E && enter[v] empty:
            queue.enq(v)
            dist[v] = dist[u] + 1
            enter[v] = T; T++
```

The algorithm keeps track on what steps a vertex is started to being processed, and when it is finished.
Additionally, it computes the distances to each vertex, and adds adjacent vertices to the list of vertices to process.
Executed on the vertex A in the graph below, the shown enter and leave numbers result.

<figure markdown>
![](../../diagrams/b/ads/bfs.png)
</figure>

### Dijkstra (non-negative edges)

To find the shortest paths in graphs without negative weighted edges, the Dijkstra algorithm can be applied. In
general, this algorithm sorts the vertices by distance to the starting vertex $s$. We use the following recursion:

$$
d(s, v_k) = \text{min}_{v_i \to v_k} \{d(s, v_i) + c(v_i,v_k)\}
$$

The algorithm keeps track of upper bounds distances to the starting vertex, until all vertices were processed at
which point we have the shortest path distances in the array $d$.

```
Dijkstra(s):
    d[s] = 0 
    for v in V && v != s: d[v] = infinity
    priorityQ = {(s, 0)}
    S = {}
    
    while S != V:
        v = priorityQ.enq()
        S.add(v)
        for w in v.adjacent() && w notin S:
            if d[v] + c(w,v) <= d[w]:
                d[w] = d[v] + c(w,v)
                priorityQ.enq(w, d[w])
```

In the following figure, the algorithm is run on a given graph. In red, we see the evolution of the costs, and in
green the order of processing.

<figure markdown>
![](../../diagrams/b/ads/dijkstra.png)
</figure>

### Bellman-Ford (negative edges)

The Bellman-Ford algorithm finds shortest paths in a graph without negative cycles. The principle of this algorithm
is to have $l$-precise bounds - bounds that are only exact for the first $l$ vertices in the shortest-path-tree. These
bounds are iteratively improved, until the costs do no longer change, which is after $(n-1)$ runs at the latest. The
recursion of this algorithm is defined as:

\begin{align}
\forall v &\in S_{\leq l} \; \backslash \; \{s\}\\
d(s,v) &= min\{d(s,u) + c(u,v) \mid u \to v,\; u \in S_{\leq l-1}\}
\end{align}

```
BellmanFord(s):
    d[s] = 0 
    for v in V && v != s: d[v] = infinity
    
    while d changed:
        for v in V:
            for u in v.adj():
                d[u] = min(d[u], d[v] + c(v, u) 
```

This algorithm can be seen in below graph where the red values show how the costs evolve.


<figure markdown>
![](../../diagrams/b/ads/bellman-ford.png)
</figure>

| A | B        | C        | D        | E        | F        | G        |
|---|----------|----------|----------|----------|----------|----------|
| 0 | $\infty$ | $\infty$ | $\infty$ | $\infty$ | $\infty$ | $\infty$ |
| 0 | 3        | 1        | 5        | 6        | 9        | 12       |
| 0 | 3        | -2       | 5        | 3        | 9        | 9        |

#### Finding negative cycles

As soon as a graph has negative edge costs, there is the potential to have negative cycles. To find if such a cycle
exists, we simply execute the Bellman-Ford algorithm. If a negative cycle exists, the algorithm will never terminate
as the weights become smaller in each iteration.

### Floyd-Warshall

The Floyd-Warshall algorithm applies a solution using the dynamic-programming technique. We define the sub-problem as
$d_{u,v}^{i}$: shortest path from $u$ to $v$ using the intermediate nodes $\{1,...,i\}$. The recursion gives us the
following case distinction.

1. $i$ is not an intermediate node: $d_{u,v}^{i} = d_{u,v}^{i-1}$
2. $i$ is an intermediate node exactly once: $d_{u,v}^{i} = d_{u,i}^{i-1} + d_{i,v}^{i-1}$
3. $i$ is an intermediate node multiple times: ignore this case if no negative cycles exist

This can be translated into pseudocode as follows. This algorithm has a running time and memory usage of $O(n^3)$

```g
FloydWarshall(V, E):
    d = int[n][n][n] // weights on diagonal are left at 0
    for u in V:
        for v in V:
            if u != v && E.contains(u,v):
                d[0][u][v] = E.contains(u,v) ? c(u,v) : infinity
    
    for i in 1..len(V): 
        for u in 0..len(V):
            for v in 0..len(V):
                d[i][u][v] = min(d[i-1][u][v], d[i-1][u][i] + d[i-1][i][v])
                
    return d[len(v)-1]
```

#### Finding negative cycles

After the algorithm has been executed, we can look at the diagonal of the DP table. If any value on this diagonal
is negative, there exists a walk from some $u$ to itself having a negative distance. In this case we can conclude
that at least one negative cycle exists.

In our algorithm, we can now conclude that: if for any $u \to v$ walk, there exists a walk (not necessarily the shortest
one) through some vertex $w$ with $d[n][w][w] < 0$, the walk $u \to v$ has a shortest distance of $-\infty$. Otherwise,
its distance is defined by $d[n][u][v]$.

### Johnson

The algorithm of Johnson applies the Dijkstra algorithm $n$-times, which is faster than using Floyd-Warshall. However,
as we have discussed before, Dijkstra only works for graphs without negative edge costs. Now, Johnson modifies the edge
costs in a clever way such that they are all positive but without changing the outcome of the algorithm.

This is for example a problem if we simply add the minimum weight in the graph to all edges. While this eliminates
negative weights, the outcome might change: say we have a shortest path crossing $n$ edges. The minimum weight is added
$n$-times to the path. There might exist some more direct path with fewer edge crossings and thus the minimum weight is
added fewer times. This path might now become the shortest path.

This problem is overcome by using a **telescoping sum** to modify the cost function. We chose some height $h(u) \in
\mathbb{R}$ for each vertex such that for any edge in the graph, the modified cost function defined below remains
positive.

$$
\hat{c}(u,v) = c(u,v) + h(u) - h(v)
$$

Due to the nature of the telescoping sum, the distance of any path between $s$ and $t$ is defined as follows. As we can
see, the final distance contains only the height of the starting and ending vertex - all other heights have canceled
out.

\begin{align}
\hat{d}(s, t) &= \sum_{i=0}^{K-1} \hat{c}(v_i,v_{i+1})\\
&= \sum_{i=0}^{K-1} \Bigg(c(v_i,v_{i+1}) + h(v_i) - h(v_{i+1})\Bigg)\\
&= \Bigg(\sum_{i=0}^{K-1} c(v_i,v_{i+1})\Bigg) + h(v_0) - h(v_k)\\
&= \Bigg(\sum_{i=0}^{K-1} c(v_i,v_{i+1})\Bigg) + h(s) - h(t)
\end{align}

To compute the height function, we add a vertex $z$ and connected it to all other vertices in the graph with $c(z,v)=0$.
The height of some vertex $u$ is then defined as the shortest path from $z$ to $u$. Due to the construction of the
graph, $h(u) \leq 0 \forall u \in V$. Further, the following holds for any $(uv) \in E$:

\begin{align}
h(v) &\leq h(u) + c(u,v)\\
0 &\leq c(u,v) +h(u) - h(v)
\end{align}

The height computation requires the graph to have **no negative cycles**.

## Minimum spanning tree (MST)

A Spanning tree is a connected subgraph with minimum weights. To find such a tree we can look for the minimum edge
incident to each vertex and add it to the MST. This, however, most certainly leaves us with a forest instead of a
tree - we now have a series of connected components. To connect the forest into a single tree, we repeatedly add the
minimum weight edge on each ZHK to the MST.

While the MST results in the minimum graph weight, it should not be confused with an alternative to find the
shortest paths.

### Uniqueness

In graphs with unique weights, the MST is **always** unique.

### Algorithms

The following chapters describe three algorithms that search for the MST.

| Algorithm | Running tim               |
|-----------|---------------------------|
| Boruvka   | $O((n + m) \cdot log(n))$ |
| Prim      | $O((n + m) \cdot log(n))$ |

#### Boruvka

This first algorithm was already described in the introduction to the MSTs. The following pseudocode describes it
in more details. In the first iteration, every vertex is its own ZHK. One iteration of the loop has a duration of $O
(n+m)$, and it is executed $log(n)$-times, as after each iteration, the number of ZHKs is halved.

```
Boruvka(V,E)
    F = {} // edge set of the spanning-tree
    while F not spanning-tree:
        S1,...,Sk = ZHK(V, F)
        e1,...,ek = minEdges(V,E)
    
        F.append(e1,...,ek)
```

#### Prim

Prim's algorithm is quite similar but here we look at one vertex after the other. Some starting vertex represents
the first ZHK, which is getting extended after each iteration until the spanning-tree is complete. In each iteration,
the minimum weighted edge incident to the ZHK is added.

```
Prim(V,E,s):
    prioQ = queue(V, infinity)
    S = {}
    d[s] = 0
    for v in V && v != s: d[v] = infinity
    
    prioQ.decrease_key(s, 0)
    while prioQ.notempty():
        v = prioQ.deq()
        S.append(v)
        
        for w in v.adj():
            if !S.contains(w):
                d[v] = min(d[v], c(v,w))
                decrease_key(v, d[v])
```

#### Kruskal

This algorithm sorts the edges by their weights (in ascending order), and then iteratively evaluates each edge. If it
connects two independent ZHKs, the edge is added to the MST. To start, each vertex is one ZHK. Implemented like this,
the algorithm will run in $O(n \cdot m)$ ($m$ iterations, and each iteration requires $n$ comparisons to check if the
vertices are in the same ZHK). The running time can be improved to $O((n+m) \cdot log(n))$ by using the data structure
_union-find_ to keep track of the ZHKs.

```
func Kruskal(V Vertex[], E Edge[]) Edge[] {
  F := make(Edge[], 0)
  ZHK := new UnionFind(V)
  E = E.sort()
  
  for e := range E {
    if !ZHK.same(e.u, e.v) {
      ZHK.union(e.u, e.v)
      F.add(e)
    }
  }
  return F
}
```

??? info "A note on the running time"

    If analysed like this, the running time of the algorithm would $O(n \cdot m)$. However, due to how the UnionFind
    algorithm is designed, an amortised analysis provides us with a better running time. 
    
    The worst case running time of a union is said to be 
    
    $$
    \Theta(\text{min}\{\text{ZHK}(u), \text{ZHK}(v)\}) = \Theta(n/2) = \Theta(n)
    $$ 
    
    This worst case, however, is only the case in a few cases as with every iteration the sets are at least being 
    doubled in size. This leads to a mean running time of $O(log(n))$.

## Layered graphs

Sometimes, it is beneficial to modify a given graph for an improved algorithm performance. An approach that is often
taken is to create multiple layers of the same graph, where the layers serve as a sort of **memory**. An example for
such a problem is finding the shortest path in a graph where $k$ cheats are allowed. A cheat is defined as
essentially setting the weight of an edge to $0$.

To solve this problem, our modified graph $G'$ contains $k+1$ copies of the original graph (including all edges).
Next, we add the edges such that for every edge $uv \in E$, we add the edge $u_iu_{i+1}$ with a weight of
$c(u_i,u_{i+1}) = 0$. Now, as long as we are in the first layer, we have cheated on $0$ edges. Once we pass a layer
down, we have cheated on one additional layer.

The problem in this modified graph $G'$ is now to find the shortest path between $s_0$ and $d_k$.

## Graphs and matrices

Matrices are actually quite close to graphs. In this chapter, we will see three possible applications on graphs. We
consider $i$-$j$-walks of length $K$ (here, we refer to the length as the number of crossed edges). We always start
with a matrix of size $n \times n$. Additionally, we know that any $i$-$j$-walk has a before-last vertex
$s$.

$L_{ij}^{(K)}$: does an $i$-$j$-walk of length $k$ exist? $L^{(1)}$ is the adjacency list.

$$
L_{ij}^{(K)} = \bigvee_{s=1}^n \big(L_{is}^{(K-1)} \land L_{sj}^{(1)}\big)
$$

$M_{ij}^{(K)}$: what's the minimum cost for an $i$-$j$-walk of length $K$? $M^{(1)}$ is the adjacency list but
instead of storing $1$ and $0$, we store the costs of edges connecting two vertices.

$$
M_{ij}^{(K)} = \text{min}_{s=1..n}\big\{M_{is}^{(K-1)} + M_{sj}^{(1)}\big\}
$$

$N_{ij}^{(K)}$: how many $i$-$j$-walk of length $K$ exist? $N^{(1)}$ is the adjacency list.

$$
N_{ij}^{(K)} = \sum_{s=1}^n N_{is}^{(K-1)} \cdot N_{sj}^{(1)}
$$

This last formula is actually a simple matrix multiplication: $N^{(K)}$ is equal to the $K$-th power of the
adjacency matrix.

### Use cases

**Counting the number of triangles** (in a graph without loops):  A triangle is defined as a $u$-$u$-walk of length $3$.
To compute the number of such triangles, we simply take the third power of the adjacency list $A_G$, and take the sum
over the diagonal: $tr(A_G^3)/3$. The division by $3$ is necessary because each triangle is found three times (once for
each of the three vertices).

**Is every vertex reachable from every other vertex** (in directed graphs): For this use case, the graph must be
slightly modified. A loop is added on each vertex, allowing us to find walks of length $\leq K$. Any two vertices, are
connected by a walk of length $\leq n-1$, if a walk exists. We can thus compute $A_G^{n-1}$ and check that all entries 
are $> 0$. 

- Naive algorithm (multiplying $n-1$-times): $O(n^4)
- Iterative squaring ($A^n = A^\frac{n}{2} \cdot A^\frac{n}{2}$): $O(n^3 \cdot log(n))$
- Strassen (dividing an $n \times n$ matrix into four $\frac{n}{2}\times \frac{n}{2}$ matrices): $\Theta(n^{2.807})$,
  useful once $n > 1000$, otherwise the constant factor is too big
- Best algorithm: $O(n^{2.3715})$, in reality not applicable due to the constant factor being too big

