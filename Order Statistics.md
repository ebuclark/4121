Say we have $n$ elements and want to find statistics on the $i$th smallest element of the sequence. We can find the minimum and maximum in $O(n)$ time, but not the *median*. We aim to find an $O(n \log n)$ or better solution for the median element in the sequence.

## Rand Select
First, we introduce a randomised algorithm for finding the $i$th smallest element of a sequence of length $n$. The idea is to use divide-and-conquer and apply QuickSort to one half of the sequence at a time. The psuedocode is as follows:

Function $RandSelect(A, p, r, i)$
- if $p = r$ and $i = 1$ return $A[p]$
- choose a **random** pivot from $A[p, \ldots, r]$ with index $q$ (i.e pick $A[q]$ for $q \in \{p, \ldots, r\})$
- sort (using QuickSort) the sub-sequence around the pivot $A[q]$ such that
	- $A[p, \ldots, (q-1)] < A[q]$
	- $A[q] < A[(q+1), \ldots, r]$
- $k = q - p + 1$
- if $k=i$ return $A[q]$ (we've sorted the $i$ smallest elements)
- if $i < k$ then return $RandSelect(A, p, q - 1, i)$
- if $k > i$ then return $RandSelect(A, q + 1, r, i - k)$
### Analysis
In the worst case, we pick the extremes as a pivot and only decrease the size of the array by $1$ at each recursive step. As we also check the conditions necessary for the 'sort' step, we impose an extra operation. So, for a constant $c$ to represent the cost of the operation
$$ T(n) = c(n + (n-1) + (n-2) + \ldots + 1) = \Theta(n^2). $$
However, this worst case rarely occurs. If we assume all the elements in the sequence are distinct, we can define a **balanced partition** as one where the ratio of the number of elements in the smaller portion and the larger piece is not greater than $1 : 9$. 

Following with the example of $10$ total 'segments', the probability that we don't pick either the smallest or largest extreme is $8/10$. So, the probability that we pick a the first balanced portion at the $k$th step is $(2/10)^{k-1} 8/10$. We can therefore use this definition to find the expected number of runs between balanced portions,
$$
\begin{align*}
	E &= 1 \times \frac{8}{10} + 2 \times \frac{2}{10} \times \frac{8}{10} + \ldots + k \times \left(\frac{2}{10}\right)^{k-1} \times \frac{8}{10} \text{ where } k \to \infty \\
	&= \frac{8}{10} \times \sum_{i=1}^k k \left(\frac{2}{10}\right)^{k-1} \\
	&= \frac{8}{10} \times S \\
	&= \frac{8}{10} \times \left(\frac{10}{8}\right)^2 & \text{using Aleks' weird trick} \\
	&= \frac{5}{4} < 2.
\end{align*}
$$

So, we on average expect two runs in between those which are a balanced partition. 
#### Sum Trick
If we have a sum
$$
\sum_{i=0}^{\infty} q^k = \frac{1}{1-q}
$$
we can differentiate both sides to get
$$
\sum_{i=1}^k k q^{k-1} = \frac{1}{(1-q)^2}.
$$
In the previous section, we just had $q = (2/10)$, so we get $(10/8)^2$ as the final answer.

### Performance of Rand Select
We've proved that there is an average of $5/4$ partitions between balanced partitions. Also, remember that the size of the array decreases to *at least* $9/10n$ at each iteration. So, the average runtime is
$$
\begin{align*}
	T(n) &< \frac{5}{4}n + \frac{5}{4} \times \frac{9}{10} n + \frac{5}{4} \times \left(\frac{9}{10}\right)^2 n + \ldots \\
	&= \frac{5}{4}n \sum_{k=0}^\infty \left(\frac{9}{10}\right)^{k} \\
	&= \frac{5}{4}n \frac{1}{\left(1 - 9/10\right)} \\
	&= 12.5n.
\end{align*}
$$
However, recall this bound is only valid for an array where all elements are distinct. If the elements are all equal, we again run into the problem of quadratic time complexity.
## Linear Time Order Statistic
We define the linear time algorithm as:

Function $Select(n, i)$:
- Split the numbers into groups of $5$
- Order each group in increasing order (by brute force)
- Collect the $j = \left\lfloor \frac{n}{5} \right\rfloor$ middle elements of each group
- $p = Select(j, \lfloor \frac{j}{2} \rfloor)$ - apply the algorithm recursively to pick the median $p$
- Use $p$ as a pivot to partition **all** the elements
- $k = |S_L|$ where $S_L = \{A[i]: A[i] < p\}$
- if $i=k$ return $p$ (we've found the median)
- if $i<k$ return $Select(k, i)$ - apply to all elements less than the pivot
- if $i>k$ return $Select(n - k, (i - k))$ - apply to all elements greater than the pivot

### Analysis
Now that we pick the median as the pivot, we improve the constant in the runtime bound. Here, we have $\lfloor(n/5)/2\rfloor = \lfloor n/10 \rfloor$ elements less than the pivot, by definition of it being the median. Then, there are 3 groups of elements which are also less than the pivot, as we group the elements in sorted groups of 5. So, we know we have $\lfloor 3n/10 \rfloor$ elements less than and greater than the pivot $p$. Then, we have to do a comparison for the elements remaining for a runtime
$$
	T(n) \leq T(n/5) + T(7n/10) + Cn,
$$
where the first term is the cost of recursion to find the median, the second the cost of recursion if $i \neq k$ and the final term the cost of sorting the elements into groups of 5 by brute force.

Using the magic of induction, we can prove that $T(n) < 11Cn$. If we assume this statement to be true, then  $T(n/5) < 11C \times n/5$ and $T(7n/10) < 11c \times 7n/10$. So, the expression above becomes,
$$
\begin{align*}
	T(n) &\leq T(n/5) + T(7n/10) + Cn \\
	&= 11C \times n/5 + 11 C \times 7n/10 + Cn \\
	&= 109C \times n/10 < 11Cn.
\end{align*}
$$
