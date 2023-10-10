Linked lists, but on steroids. We achieve an average $O(\log n)$ operations overall (insertion, deletion and search) but without the complexity of {AVL, Red-Black} trees or other complex data structures.

The idea is to make *shortcuts* or 'skips' between several elements in the list, while keeping the list sorted. These skips allow us to skip many elements which may not be candidates for a search or removal, reducing the overall traversal time of the list.

![[Pasted image 20231010190321.png]]

Here, we can see a skip list with multiple levels, where higher levels have larger skips (i.e we skip more elements). Choosing the number of skip levels we give an element is a non-trivial problem - as if we choose a fixed interval for each level, we effectively break the protocol when removing an element from the list. 

We therefore look to randomisation to choose the number of levels we place a node on. We toss a fair coin and stop when we get a tail (say at $k$ flips). Then, we place this node on $k$ skip levels in the list.
## Analysis
### Expected Number of Levels for an element
The probability that an element is on a level $i$ is simply $1/2^i$ where we have an average of $n/2^i$ elements per level. So, if we count the first level (i.e level $0$) we have the total number of levels $L_j$ for an element $j$ as,
$$
\begin{align*}
	\mathbb{E}(L_j) &= \sum_{i=0}^\infty \frac{i+1}{2^{i+1}} = \sum_{i=1}^\infty \frac{i}{2^i} \\
	&= 2.
\end{align*}
$$
Achieving this result comes from evaluating the sum as follows,
$$
\begin{align*}
	\text{Let } S &= \sum_{i=1}^\infty \frac{i}{2^i} = \frac{1}{2} + \frac{2}{2^2}+ \frac{3}{2^3} + \ldots \\
	\frac{1}{2}S &= \frac{1}{2^2} + \frac{2}{2^3}+ \frac{3}{2^4} + \ldots. \\
\end{align*}
$$
Then,
$$
\begin{align*}
	S - \frac{1}{2}S &= \frac{1}{2} - \frac{1}{2^2} + \frac{2}{2^2} - \frac{2}{2^3} + \frac{3}{2^3} - \frac{3}{2^4} + \ldots \\
	&= \frac{1}{2} + \frac{1}{2^2} + \frac{1}{2^3} + \frac{1}{2^4} + \ldots \\
	&= \frac{1}{2} \times \frac{1 - (1/2)^n}{1 - 1/2} \, \text{ where } n \to \infty \\
	&= 1,
\end{align*}
$$
therefore,  $S = 2$.

### Number of Elements on a Level
Now, we find the converse of the previous result, the expected number of elements on a level. If we define the number of elements on a level $i$ as $N_i$, we can use the Markov inequality to find the probability that we have at least one element on the level,
$$
P(N_i \geq 1) \leq \frac{\mathbb{E}(N_i)}{1} = \frac{n}{2^i}.
$$
Therefore, the probability to have an element on level $k \log n$ is 
$$
	\frac{n}{2^{k \log n}} = \frac{n}{n^k} = \frac{1}{n^{k-1}}.
$$
So, what is the expected value of the number of populated levels (say $N_L$) when we have $k \log n$ levels in total?

$$
\begin{align*}
	\mathbb{E}(N_L) &\leq \sum_{i=1}^n \frac{k}{2^{k-1}} \, \text{ by the previous result} \\
	&= \left(\frac{n}{n-1}\right)^2 \, \text{ using derivative identity} \\
\end{align*}
$$
which is slightly larger than $\log n$. 

Also, the expected number of elements *between* two levels is simply,
$$
	\frac{0}{2} + \frac{1}{2^2} + \frac{2}{2^3} + \frac{3}{2^4} +\ldots = 1
$$
where we therefore check $2$ elements (the one element in between and final) on average for operations which use skips. 

### Time and Space Complexity
As we've derived before we have around $2 \log n$ levels and check around $2$ elements per level for a time complexity of $O(\log n)$.

For space complexity, we store
$$
	O\left(\sum_{i=0}^\infty 2(i+1) \frac{n}{2^{i+1}}\right) = O\left(2n \sum_{i=0}^\infty \frac{i+1}{2^{i+1}}\right) = O(4n) \approx O(n)
$$
units for the entire list. We derive the first form as we need $2(i+1)$ links for a single element on a level $i$ for the doubly linked list - we do this $n$ times hence the coefficient. As we've established, the probability of these links decreases as the level is higher with the form $1/2^{i+1}$.
