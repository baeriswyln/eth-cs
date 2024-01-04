# Search and Sort

## Search algorithms

We have seen two search algorithms: one working on un-sorted arrays, and one working on sorted arrays.

### Unsorted array (Linear search)

The linear search algorithm is pretty simple: we iterate through all elements in the array and stop once we found the
element we were looking for. This operation has $O(n)$, and it's the best we can do on unsorted arrays. Because, in case
we are looking for an element that does not exist in the array, we must look at every element to be sure of it.

### Sorted array (Binary search)

The binary search algorithm requires the array to be sorted, such that $A[i-1] \leq A[i] \leq A[i+1] \forall i
\in [1,n-2]$.

The algorithm always compares the value $b$ its looking for with the central element of the array $A[n/2]$. If this
value is bigger than $b$, the algorithm recursively calls itself by passing the remaining array to the left of the
central element. If it is smaller than $b$, the recursion is run on the right part, and if $A[n/2] = b$, we already
found the solution.

Binary search runs in $O(log_2(n))$, and this is the best we can do. The proof for this is based on the fact that a
decision tree of any search algorithm has $n+1$ leafs (one for each value in the array, and one additional element
signaling that the value is not present in the array). The depth of such a binary decision tree is $log_2(n)$ and thus
any algorithm must make at least that many comparisons.

## Sorting algorithms

In this chapter we will be looking at a series of sorting algorithms. The below table lists all algorithms and notes
their complexity in terms of number of compare and copy operations, as well as the additional memory being required.

| Algorithm      | Compare             | Copy                | Extra memory |
|----------------|---------------------|---------------------|--------------|
| Bubble sort    | $O(n^2)$            | $O(n^2)$            | $O(1)$       |
| Selection sort | $O(n^2)$            | $O(n)$              | $O(1)$       |
| Insertion sort | $O(n \cdot log(n))$ | $O(n^2)$            | $O(1)$       |
| Merge sort     | $O(n \cdot log(n))$ | $O(n \cdot log(n))$ | $O(n)$       |
| Quick sort     | $O(n \cdot log(n))$ | $O(n \cdot log(n))$ | $O(1)$       |
| Heap sort      | $O(n \cdot log(n))$ | $O(n \cdot log(n))$ | $O(1)$       |

We have proven in the lecture that the best possible complexity for (comparison-based) sorting algorithms is
$O(n log(n))$. The proof is similar to the one for sorting algorithms. The number of nodes in the decision tree is
greater or equal the number of possible outputs, which is $n!$ (there are $n!$ many different permutations of an array).
The depth of such a decision tree is $log(2!)-1 \geq \Omega(n log(n))$, and it requires thus that many comparisons.

### Bubble sort

**Invariant** $I(k)$: the $k$ biggest elements are at the correct (and final) position.

This algorithm iterates many times (at most $n$ to be exact) through the entire array. In one iteration, it always
compares neighbouring entries. If they are in the wrong order, the elements are swapped. This is done until in one
iteration, no swap was made, or at most $n$-times.

**Complexity**: Compare: $\Theta(n^2)$, Swap: $O(n^2)$

??? tip "Pseudocode"

    ```go
    func BubbleSort(A int[], n int){
        for _ := range n {
            for i := range n-1 {
                if(A[i] > A[i+1]) A[i+1], A[i] = A[i], A[i+1]
            }
        }
    }
    ```

### Selection sort

**Invariant** $I(k)$: the $k$ biggest elements are at the correct (and final) position.

Selection sort searches the biggest element in the array, and swaps it with the last element. In the next iteration,
it searches for the next largest element, and puts it right in front of the largest element. This process is repeated
$n$ times, at which point the array is sorted.

**Complexity**: Compare: $\Theta(n^2)$, Swap: $O(n)$

??? tip "Pseudocode"

    ```go
    func SelectionSort(A int[], n int){
        arg_max := 0
        
        for i := range n{
            for j := range (1,n-i) {
                if (A[arg_max] < A[j]) arg_max = j
            }
            A[arg_max], A[n-i-1] = A[n-i-1], A[arg_max] 
        }
    }
    ```

### Insertion sort

**Invariant** $I(k)$: the first $k$ elements are correctly sorted.

In iteration $k$, the algorithm takes the element $A[k]$ and places it in $A[0:k]$ such that the ordering in this
subarray is correct. Let $j$ the index in the subarray where the element $A[k]$ will be placed. All elements $A[j:k-1]$
are required to move one position to their right. The correct position in the subarray can be found using the _binary
search_ algorithm, as it is known to be sorted.

**Complexity**: Compare: $O(n \cdot log(n))$, Swap: $O(n^2)$

??? tip "Pseudocode"

    ```go
    func InsertionSort(A int[], n int){
        for k := range A {
            k_val := A[k]
            j := bin_search(A[0:k])
            
            for i := k; i > j; k-- {
                A[i] = A[i-1]
            }
            A[j] = k_val
        }
    }
    ```

### Merge sort

**Recursion**: Both halves of the array are correctly sorted.

Merge sort is the only algorithm taking a significant additional amount of space, as it must temporarily store an array
of size $n$ in addition to the original array. It uses the _divide and conquer_ approach: the array is halved and
recursively sorted. Both halves are than merged together which can be done in $O(n)$ because both halves are already
sorted.

**Complexity**: Compare: $O(n \cdot log(n))$, Swap: $O(n \cdot log(n))$ (one level is $O(n)$, and there are $log(n)$
recursion levels)

??? tip "Pseudocode"

    ```go
    func MergeSort(A int[], l int, r int) {
        if l < r {
            mid = (l + r)  / 2
            MergeSort(A, l, mid)        // sort left halve
            MergeSort(A, mid + 1, r)    // sort right halve
            Merge(A, l, mid, r)         // merge sorted halves
        }
    }

    func Merge(A int[], l int, mid int, r int) {
        B := int[r - l + 1]
        i, j, k := l, mid + 1, 1

        while i <= mid && j <= r {
            if A[i] < A[j] {
                B[k] = A[i]
                k++; i++;
            } else {
                B[k] = A[j]
                k++; j++;
            }
        }
        A[l:r] = B
    }
    ```

### Quick sort

**Recursion**: All elements left to the _pivot_ are smaller, all elements to the right are bigger.

The QuickSort algorithm is a randomized and recursive algorithm. In every recursion step, a random **pivot** element is
chosen. Next, the algorithm iterates through the array, making sure that the pivot element is put at a position where
all elements to its left are smaller, and all elements to its right are bigger. This will also be the final position of
the pivot. This procedure is then recursively applied on both sides of the array.

Due to its probabilistic nature, the algorithm has a worst case complexity of $O(n^2)$. However, this is only the case
if the chosen pivot is always the biggest or smallest element of the array. As this is very unlikely, the mean
complexity is $O(n \cdot log(n))$.

**Complexity**: Compare: $O(n \cdot log(n))$, Swap: $O(n \cdot log(n))$

??? tip "Pseudocode"

    ```go
    func QuickSort(A int[], l int, r int) {
        if l < r {
            pivot_index = MoveAroundPivot(A, l, r)
            QuickSort(A, l, pivot_index-1)
            QuickSort(A, pivot_index+1, r)
        }
    }

    func MoveAroundPivot(A int[], l int, r int) {
        // use the last element in the curernt array section as pivot. 
        // can also be any random element
        pivot = A[r]         
        il, ir := l, r-1

        while(il < ir) {
            // search for next pair of elements that can be swaped
            while(A[il] <= pivot && il < r) il++
            while(A[ir] > pivot && ir > l) ir--
            if(il < ir) {
                A[il], A[ir] = A[ir], A[il]
            }
        }
        // put pivot element in the middle
        A[il], A[r] = A[r], A[il]

        return il
    }
    ```

??? info "Running QuickSort deterministically in $0(n \cdot log(n))$"

      It is possible to have a deterministic running time of $0(n \cdot log(n))$, if we are able to find the median (thus
      the pivot element) in $O(n)$. In reality this is not one for the QuickSort algorithm.
      
      The QuickSelect algorithm finds the median recursively in $O(n)$. Actually, it is able to find the $i$-th smallest
      element, and the median is then a special case of this algorithm.
      
      We need to find a pivot that is not too close to the ends of the given array. In fact, it must not be under the first
      $\epsilon n$ elements. In the worst case, the recursion runs on $(1-\epsilon)n$ elements. Using the master-theorem,
      we can compute a running time of $O(n)$:

      - $T(n) \leq T((1-\epsilon)n) + cn$
      - $\leq T((1-\epsilon)^2n) + c(1-\epsilon)n + cn$
      - $\leq T((1-\epsilon)^3n) + c(1-\epsilon)^2n + c(1-\epsilon)n + cn$
      - $...$
      - $\leq T(1) + cn \sum_{i=0}^{...}(1-\epsilon)^i \leq O(n)$
      
      To achieve this, the algorithm splits the input into groups of five elements, and then finds the median of these
      elements in a linear time factor.

      $\text{QuickSelect}(A,i)$

      1. Select pivot $p$:
          1. Split $A$ into groups of five
          2. Find the median of each group, and put them into $A'$ of length $\lceil \frac{n}{5} \rceil$
          3. $p = \text{QuickSelect}(A',\frac{1+ \lceil n/5 \rceil}{2})$
      2. $k = \text{Split}(A[1..n],p)$, split on pivot where $k$ is the pivot index
      3. Recursion:
          - $i=k$: return $k$
          - $i<k$: $\text{QuickSelect}(A[1..k-1],i)$
          - $i>k$: $\text{QuickSelect}(A[k+1..n],i-k)$

      With this algorithm we find a pivot where at least $3n/10$ elements are smaller and $3n/10$ elements are bigger than
      $p$. Our recursion finds the pivot in $A'$ to be in the middle, and thus $n/10$ elements are smaller than $p$ (half of
      $A'$s size). Additionally, we know that for each element in $A'$, there are two elements that were left out that were 
      smaller than those other medians to the left of $p$.
   
      To find the running time, we use the master-theorem. We have a double recursion. The recursion in 1c is $T(n/5)$, the
      recursion in 3 is $T(7n/10)$, giving us $T(n) \leq T(n/5) + T(7n/10) + cn$. We state that $T(n) \leq 10cn$, which we 
      prove through induction: 
      
      - $T(n) \leq T(n/5) + T(7n/10) + cn$
      - $\leq 10c \frac{n}{5} + 10cn\frac{7}{10} + cn$
      - $=10c(\frac{9}{10}n) + cn$
      - $= 10cn \leq O(n)$

### Heap sort

**Recursion**: The root of the heap is the biggest element of the array.

Unlike all other sorting algorithms, this one makes use of an alternative data structure. In fact, the sorting algorithm
only uses the basic operations of the data structure to sort an array. The data structure at stake is the **Max-heap**.
This is a complete binary tree where the keys are the values of the array. The key of each vertex must be greater or
equal to all keys of its descendants. More on this data structure is to be found in the respective chapter.

As mentioned, the algorithm is fairly simple. The max-heap is created using the array, and then the maximum is extracted
until the hea is empty. The order of these maximums is the reverse order of the array. Due to the max-heaps structure,
the sorting algorithm has memory accesses that are jumping all over the memory, which somewhat reduces the speed.

As shown in the chapter about the max-heap data structure, the operations `Insert` and `ExtractMax` both have a time
complexity of $log(n)$. The complete time complexity comes from the fact that both operations are executed $n$-times to
get the sorted array. Although the algorithm below would let you think that additional memory is required, some clever
thinking allows the array to be sorted in place by using it to store the heap.

**Complexity**: Compare: $O(n \cdot log(n))$, Swap: $O(n \cdot log(n))$

??? tip "Pseudocode"

    ```go
    func HeapSort(A int[], n int){
        H := new Heap()
        for v := range A {
            H.Insert(v)
        }
        for i := range n-1..0 {
            A[i] = H.ExtractMax()
        }
    }
    ```