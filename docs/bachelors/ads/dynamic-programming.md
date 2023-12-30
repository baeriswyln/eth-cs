# Dynamic Programming (DP)

Dynamic programming is an approach to solving problems where results of previous computations are reused. Such results
are stored inside the _DP-table_. If applied correctly, the running time of an algorithm can be greatly reduced, and 
often even the code gets simpler. 

To find such algorithms, a suitable **sub-problem** is defined - it generally describes the meaning of an entry in 
the DP-table. Using the sub-problem, we can then define the **recursion** describing how such han entry can be 
computed from previously established values. 

## Approaches 

To solve a sub-problem, we have seen two general approaches:

**Top-down (memoization)**: this approach is often used with recursive algorithms. Essentially, before returning the 
result, such an algorithm stores the computed value inside a memoization table such that later function calls for 
the same value can simply return the value of a table lookup.  

??? info "FIB using a top-down approach"

    Please note that the `memo` array is a global array whose values is not stored in the function. 
    
    ```go
    func fib(n) {
        memo[n] = 1
        if n > 2 {
            memo[n] = fib(n-1) + fib(n-2)
        }
        
        return memo[n]
    }
    ```

**Bottom-up**: such algorithms use an iterative approach by starting with the base case. The value of this base case 
is stored in a table. The computation of the subsequent values are based on the previously computed values. In the 
case of fibonacci, we have the base cases `fib(1)=1` and `fib(2)=1` which are then used to compute `fib(3)`. 

??? info "FIB using a bottom-up approach"

    ```go
    func fib(n) {
        dp := make(int[], n)
        dp[0], dp[1] = 1, 1
        
        for i := range 2..n {
            dp[i] = dp[i-1] + dp[i-2]
        }
        return dp[n]
    }
    ```

??? info "Naive approach to computing the fibonacci number"

    The following algorithm runs in $\Omega(2^{\frac{n}{2}})$.
    
    ```go
    func fib(n) {
        if n <= 2 {
            return 1
        }
        return fib(n-1) + fib(n-2)
    }
    ```
    
    Running time: 
    
    \begin{align}
    T(n) &= T(n-1) + T(n-2) + c\\
    & \geq 2 \cdot T(n-2)\\
    & \geq 4 \cdot T(n-4)\\
    & ...\\
    & \geq \Omega(2^{\frac{n}{2}})
    \end{align}

## Solution description

A solution to a DP-problem can be described using the following six points. 

1. **Dimensions of the DP table**: What are the dimensions of the DP table?
2. **Sub-problems**: What is the meaning of each entry?
3. **Recursion**: How can an entry of the table be computed from previous entries? Justify why your recurrence 
   relation is correct. Specify the base cases of the recursion, i.e., the cases that do not depend on others.
4. **Calculation order**: In which order can entries be computed so that values needed for each entry have been 
   determined in previous steps?
5. **Extracting the solution**: How can the solution be extracted once the table has been filled?
6. **Running time**: What is the running time of your solution

## Examples by problem definitions

We are now looking at seven problem examples that can be solved through a DP approach.

### Maximum subarray sum

In an array, we are looking for the biggest sum built from consecutive cells.

??? tip "Solution description"

    1. **Dimensions of the DP table**: $n$
    2. **Sub-problems**: $DP[j]$ max subarray sum using the first $j$ cells of the input array. 
    3. **Recursion**: 
        - BC: $DP[1] = max\{0, A[1]\}$
        - $DP[j] = max\{A[j], A[j] + DP[j-1]\}$
    4. **Calculation order**: bottom-up
    5. **Extracting the solution**: $max\{DP\}$
    6. **Running time**: $O(n)$

### Jump Game

Given an array of positive integers $A$. We start on position $1$ and try to reach the end of the array with as few 
jumps as possible. The number at the current position defines how far we can jump in the array. 

??? tip "Solution description"

    1. **Dimensions of the DP table**: $n$
    2. **Sub-problems**: $DP[i]$ max index reachible in $i$ jumps
    3. **Recursion**: 
        - BC: $DP[1] = A[1]$
        - $DP[i] = max\{j + A[j] \mid DP[i-2] \leq i \leq DP[i-1]\}$
    4. **Calculation order**: bottom-up
    5. **Extracting the solution**: $min\{i \mid DP[i] \geq n\}$
    6. **Running time**: $O(n)$

### Longest common subsequence

Given two strings $A$ with length $n$ and $B$ with length $m$, we are searching for the length of the longest common 
sequence. The letters must be in the correct order, but between the letters we allow for whitespaces. 

??? tip "Solution description"

    In this solution, we start indexing the DP-table at 0.

    1. **Dimensions of the DP table**: $(n+1) \times (m+1)$
    2. **Sub-problems**: $DP[i][j]$ longest common subsequence using the first $i$ letters from $A$ and the first 
       $j$ letters of $B$
    3. **Recursion**: 
        - $BC: DP[0][j]=DP[i][0]=0$
        - $A[i] = B[j] \Rightarrow DP[i][j] = DP[i-1][j-1] + 1$
        - $A[i] \neq B[j] \Rightarrow DP[i][j] = max\{DP[i-1][j], DP[i][j-1]\}$
    4. **Calculation order**: bottom-up
    5. **Extracting the solution**: $DP[n][m]$
    6. **Running time**: $O(n \cdot m)$

    The clue here in the recursion is to define what we do with the letters from the strings A and B. For an index 
    $i$ and $j$, there are essentially three possibilities.

    1. Use both $A[i]$ and $B[j]$. This is only possible if both letters are identical.
    2. Don't use $A[i]$: $DP[i][j] = max\{DP[i-1][j], DP[i][j-1]\}$
    2. Don't use $B[j]$: $DP[i][j] = max\{DP[i-1][j], DP[i][j-1]\}$

### Edit distance

Given two strings $A$ with length $n$ and $B$ with length $m$, what's the minimum of operations required to convert 
the string $A$ into the string $B$? The allowed operations are: insert a new letter, delete a letter, change a letter.

??? tip "Solution description"

    1. **Dimensions of the DP table**: $(n+1) \times (m+1)$
    2. **Sub-problems**: $DP[i][j]$ the number of operations required to convert the string $A[1..i]$ to $B[1..j]$
    3. **Recursion**: 
        - $BC: DP[i][0] = i, DP[0][j] = j$ (converting the string $A$ into the empty string requires the deletion of 
          $i$ letters)
        - $A[i] = B[j] \Rightarrow DP[i][j] = DP[i-1][j-i]$
        - $A[i] \neq B[j] \Rightarrow DP[i][j] = min\{DP[i-1][j]+1, DP[i][j-1]+1, DP[i-1][j-1]+1\}$
    4. **Calculation order**: bottom-up
    5. **Extracting the solution**: $DP[n][m]$
    6. **Running time**: $O(n^2)$

    We are again making a case distinction:
    
    1. Letter A[j] is used as is in B[j]: only possible if the letters are identical. $DP[i][j] = DP[i-1][j-i]$
    2. Letter A[i] is converted into B[j]: $DP[i][j] = DP[i-1][j-1]+1$
    3. Letter A[i] is deleted: $DP[i][j] = DP[i-1][j]+1$
    4. Letter B[j] is inserted: $DP[i][j] = DP[i][j-1]+1$

### Subset sum

Given an array of $n$ integers $A$ and an integer $b$ we are looking for the subset of integers in $A$ whose sum is 
equal to $b$ (if one exists). 

??? tip "Solution description"

    The approach first completely ignores what elements are used to reach the sub. We are simly computing if it is 
    possible at all to reach the sum. To get one possible subset, we use backtracking.

    1. **Dimensions of the DP table**: $(n+1) \times (b+1)$
    2. **Sub-problems**: $DP[i][j]$ is it possible to reach the sum $j$ using a subset from the first $i$ entries of 
       $A$ 
    3. **Recursion**:
        - BC: $DP[i][0] = True$ it is always possible to have a sum of $0$
        - $DP[i][j] = DP[i-1][j] \vee DP[i-1][j-A[i]]$
    4. **Calculation order**: bottom-up
    5. **Extracting the solution**: $DP[n][b]$
    6. **Running time**: $O(nb)$ this is also called **pseudo-polynomial** because the running time now also depends 
       on the size of an input number.  

### Knapsack

Given a rucksack with a weight limit $W$ and $n$ objects all having a weight $w_i \in \mathbb{N}$ and a value $v_i 
\in \mathbb{N}$, we are looking for the subset of objects such that the sum of the values is maximal while staying 
below the weight limit of the rucksack.

??? tip "Solution description"

    1. **Dimensions of the DP table**: $n \times W$
    2. **Sub-problems**: $DP[i][j]$ the maximum achievable value with the first $i$ items while not surpassing a weight 
       limit of $j$. 
    3. **Recursion**: $DP[i][j] = max\{DP[i-1][j], v_i + DP[i-1][j-w_i]\}$
    4. **Calculation order**: bottom-up
    5. **Extracting the solution**: $DP[n][W]$
    6. **Running time**: $O(n \cdot W)$, again this is a **pseudo-polynomial** algorithm.
    
    The above approach tries to maximize the value for a certain weight. A slightly modified version of the problem 
    searches for the minimum weight required to reach a combined value of $V$. This problem's recursion is defined as 
    $DP[i][j] = min\{DP[i-1][j], w_i + DP[i-1][j-v_i]\}$ and the running time is $O(n \cdot V)$.

Currently, there is no known algorithm improving the running time. However, by slightly modifying the problem, we 
are able to improve the running time (here, we take the modified version "minimum weight to  reach a combined value 
of $V$"): Instead of knowing the exact values, are underestimating the values through an approximation. The values 
$v_i$ are replaced with $\tilde{v_i} = K \lfloor \frac{v_i}{K} \rfloor, K \in \mathbb{N}$. All values are rounded 
down to the next closest multiple of $K$. The algorithm essentially stays identical, but now we are only required to 
fill out k-th column.

This approximated solution has a running time of $O(\frac{n \tilde{V}}{K}) \leq O(\frac{n^2 v_{max}}{K})$ and a 
precision of $V_{\tilde{opd}} \geq V_{opd} \cdot (1 - \frac{nK}{v_{max}})$.

By defining $K = \frac{\epsilon v_{max}}{n}$ where $\epsilon > 0$ is small, we can define the value of our 
approximated solution also as $V_{\tilde{opd}} \geq V_{opt} (1 - \epsilon)$ and the running time as $O(\frac{n^3}
{\epsilon})$

### Longest ascending subsequence 

Given an array of $n$ (supposedly unique) integers $A$ we search for the longest ascending subsequence (LAS). The 
integers in the subsequence must always grow, and the subsequence is not necessarily chained together in the given 
array - we allow for values in between that are not used in the subsequence. 

??? tip "Solution description"

    1. **Dimensions of the DP table**: $n \times n$
    2. **Sub-problems**: $DP[i][j]$ the smallest ending possible for a sequence of length $j$ using the first $i$ values 
       of $A$.
    3. **Recursion**:
        - BC: $DP[1][1] = A[1]$, $DP[1][j>1] = \infty$
        - $DP[i-1][j-1] < A[i] < DP[i-1][j]$ ($A[i]$ can be used, and it is better than the previous minimum for this 
          length): $DP[i][j] = A[i]$
        - Otherwise: $DP[i][j] = DP[i-1][j]
    4. **Calculation order**: bottom-up
    5. **Extracting the solution**: $DP[n][j]$ where $j$ is the largest value for which $DP[n][j] \neq \infty$
    6. **Running time**: $O(n^2)$
    
    The exact subset can be found through backtracking. 
    
    The same approach can be modified slightly by reusing the first column in every iteration to receive a running time 
    of $O(n \cdot log(n))$: the numbers in the DP table are always sorted from smallest to largest, and in every iteration, 
    exactly one entry is modified. If the next value in the input array is $x$, we can use the binary search algorithm 
    to find the next smallest value in the DP table, and then replace the value to its right with $x$.   

## Backtracking

A DP-table often contains only number values. For example, in the "Longest common subsequence", the DP-table stores 
the length of the subsequence. However, the table still allows to extract the subsequence itself through strategic 
backtracking through the table. 

We start by looking at the value of the final result and search our way back through the array to the starting 
position. 