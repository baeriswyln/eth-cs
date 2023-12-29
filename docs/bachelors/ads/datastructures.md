# Datastructures

## Datastructures vs Abstract datatype

## List

## ADT Dictionary

### Binary Tree

### AVL Tree

## Queue

## (Max)-Heap

The data structure Max-Heap has a clever way to sort its data allowing access to the maximum element in constant time. 
It is for example used in the sorting algorithm _HeapSort_ and consists of the two main operations: `Insert` and 
`ExtractMax`.

We have a **Heap condition** that specifies how the heap must be constructed to be valid. In the case of the max-heap, 
this condition specifies that the key of an element must be greater or equal to all keys of its descendants.

### In-memory representation

The heap is stored as an array. The indexes can be used to determine an element's position in the binary tree underlying
the heap: the direct children of the element $i$ are to be found at index $2i$ and $2i + 1$ (requires indexing to start
at $1$).

### Operations

Both operations shown below always require the heap-condition to be met before the operation is started. As the tree has 
a depth of $log(n)$, and the elements are only moved vertically, a maximum of $log(n)$ swaps can be done in both cases. 
It follows, that their time complexity is $O(log(n))$.

**Insert**

1. Put element at the end of binary tree (in memory: the end of the array)
2. Compare the element with its parent. If the parent is smaller, swap the keys. 
3. Repeat step 2 until heap-condition is met. 

**ExtractMax**

1. Get the heap's maximum from the root. 
2. Replace the root's key with the key of the last leaf in the binary tree. 
3. Swap the new root with the biggest of its two children. 
4. Repeat step 3 until both children are smaller or equal to itself in which case the heap-condition is met again.

**CreateHeap** is another possible operation. It can be done by using the _Insert_ operation on all elements of the 
array. Another possibility is to reuse the given array and use it as the heap. At the beginning, the heap-condition is
(most certainly) not met. Next, we start looking at the last complete level of the binary tree and let all keys _sink_.
This means, the elements are compared to its children, and if necessary, swapped with the bigger of the two. This is 
then repeated for all levels up to the root of the heap, resulting in a heap meeting its heap-condition. The _sinking_
process must be done all the way to the bottom of the tree. 

## Stack