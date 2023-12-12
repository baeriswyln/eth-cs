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

### Merge sort

### Quick sort

### Heap sort

**Invariant** $I(k)$:

**Complexity**: Compare: $O()$, Swap: $O()$

??? tip "Pseudocode"

    ```go
    func bubbleSort(A int[], n int){

    }
    ```