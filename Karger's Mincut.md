### Introduction and Motivation
First, we'll start with some definitions to frame the min-cut problem itself. We assume that we are given a weighted un-directed connected graph $G = (V, E)$ (where $V$ are the vertices and $E$ the edges). 

A *cut* is defined as a partition of the graph into two disjoint subsets $T = (X,  Y)$ where $X \cup Y = V$ (i.e the cut separates the graph into two disjoint subsets of its vertices). The capacity of a cut is defined as the sum of weights of the edges it spans, that is, the sum of edges which have one end in $X$ and one end in $Y$. Formally the capacity of $T$ is $C(T) = \sum_{u \in X, \, v \in Y} w(u, v)$ for a cut $T = (X, Y)$. A cut is minimal only if it has the lowest capacity among all possible cuts.

![[Pasted image 20231029091954.png]]

Now, our task is to *find* the min-cut given a graph $G$. One rudimentary approach is to use a max-flow algorithm (using the max-flow min-cut theorem, where max-flow = min-cut) for this graph. Here, we have to pick an arbitrary point as the source and cycle through all other points as the sink, then use the maximum resultant flow as our result. However, max flow algorithms are slow, with complexity of $O(|V|^3)$ for Ford-Fulkerson. This means that running this algorithm for $n = |V|$ other vertices we have a total time complexity of $O(n^4)$.
### Karger's Mincut
Karger's algorithm takes a randomised approach to the min-cut problem to reduce the average time complexity. We introduce a main *operation* which we repeatedly use to converge toward a min-cut.
#### Edge Contraction
The first operation is one of edge contraction, where we *fuse* two edges together and combine their weights. If we have an edge $e(u, x)$ and another edge $e(x, v)$, we can fuse together $u$ and $v$ into a vertex $[uv]$ with a resultant edge $e([uv], x)$.

![[Pasted image 20231029093109.png]]

We typically refer to this new graph as $G_{uv}$ to represent the contraction.
##### Claim 1
If we contract an edge to form $G_{uv}$ from an original graph $G$, the minimal cut in $G_{uv}$  always the same capacity as the one in $G$ only if $u$ and $v$ are in the same side of the cut.
![[Pasted image 20231029093419.png]]
The proof for this claim should be obvious. If we contract an edge which is not in the cut, we never alter the capacity of the cut at all.
##### Claim 2
If we contract an edge where $u$ and $v$ lie on opposite sides of the cut, the minimal cut in $G_{uv}$ is larger than or equal to the size of the cut in $G$.
![[Pasted image 20231029093848.png]]
Again, this should be obvious as we can never decrease the capacity of the cut since the sum of the weights are preserved in contraction.
##### The Main Operation
We can combine these claims in a creative way to find the min-cut for the original graph $G$. If we first contract an edge which has its vertices on opposite sides of the cut, we preserve the capacity of the cut itself. Then, we can split the now *fused* vertex back out into it's respective parts with the contracted edge now removed. 
![[Pasted image 20231029094511.png]]
Now, using our two claims, the final cut $T_2 = T_1 \geq T$. It may seem like we are only increasing the cut as we contract edges, but remember, we don't actually *have* a cut in this algorithm as this is what we are finding.

We randomly pick edges to contract and where the vertices lie with respect to the minimal cut is irrelevant, since we can *possibly* be running into the case where $T_2 = T_1 = T$ and we find the minimal cut as the final answer.
#### The basic algorithm
As stated before, we rely on the two claims above to finally arrive at a solution where the weight of the final contracted edge contains the capacity of the minimal cut (due to the fact that $T_1 \geq T$ if we contract an edge which lies in the cut). So, we can detail a basic approach as follows:
- Randomly pick an edge with a weighted probability 
$$
P(e(u, v)) = \frac{w(u, v)}{\sum_{e(p, q) \in E} w(p, q)}
$$
- Contract this edge to form a new graph $G_{uv}$
- Apply the previous operations to $G_{uv}$ until we have one edge left
- The weight of the final edge is the estimate for the minimal cut

We can run an analysis on this algorithm to calculate it's probability of success. First define,
$$
P(MC(G_{uv}) > MC(G)) \leq P(e(u, v) \in M)
$$
where $MC$ is a function which calculates the minimum capacity cut for a graph and $M$ is the set of edges in the minimum cut of $G$. This is the probability that we *increase* the minimal cut after contraction, which only occurs if the edge we are contracting is in the minimal cut.

Note that the probability of picking and edge $e(u, v)$ in the minimal cut is defined as
$$
	P(e(u, v) \in M) = \frac{\sum \{w(j, k) : e(j, k) \in M\}}{\sum_{m \in V, \, o \in V} w(m, o)}.
$$
Now, we know that for a vertex $v \in V$
$$
	\sum_{u \in V} w(v, u) \geq MC(G)
$$
by definition. If we sum these inequalities $n$ times (for every point) we get
$$
\begin{align*}
	2 \sum_{v \in V, \, u \in V} w(v, u) &\geq n \cdot MC(G) \\
	\sum_{v \in V, \, u \in V} w(v, u) &\geq \frac{n}{2} \cdot MC(G)
\end{align*}
$$
where the factor of $2$ comes from the fact that we double count the edge sums since $G$ is undirected. Then, we can derive the inequality
$$
	P(e(u, v) \in M) = \frac{\sum \{w(j, k) : e(j, k) \in M\}}{\sum_{m \in V, \, o \in V} w(m, o)} \leq \frac{MC(G)}{n/2 \cdot MC(G)} = \frac{2}{n}.
$$
Therefore, we finally have the inequality
$$
P(MC(G_{uv}) > MC(G)) \leq P(e(u, v) \in M) \leq \frac{2}{n}.
$$
We can then use this bound to find the probability that the final contracted edge of a graph $G$ with $n$ vertices is equal to the minimal cut of $G$ (call this probability $\pi$ for simplicity). Note, we can only make $n - 2$ contractions in total. Now we apply the inverse of the identity to find,
$$
\begin{align*}
	\pi &= P(MC(G_{n-2}) = MC(G)) \\
	&= \prod_{i=1}^{n-2} P(MC(G_{i}) = MC(G_{i - 1})) \\
	&\geq \left(1 - \frac{2}{n}\right) \left(1 - \frac{2}{n - 1}\right)\left(1 - \frac{2}{n - 2}\right) \cdots \left(1 - \frac{2}{3}\right) \\
	&= \frac{n-2}{n} \frac{n-3}{n-1}\frac{n-4}{n-3} \cdots \frac{3}{5}\frac{2}{4}\frac{1}{3} \\
	&= \frac{2}{n(n-1)} & \text{cancellation of 2nd terms}
\end{align*}
$$
we get to the last step by realising that the denominator of every term contains the numerator of 2 terms before it. This implies that we have a bound $\pi = \Omega\left(\frac{1}{n^2}\right)$ on the probability of success for Karger's minimal cut. Now, if we run contract only $n/2$ times, we can derive the bound
$$
\begin{align*}
	\pi &= P(MC(G_{n-2}) = MC(G)) \\
	&= \prod_{i=1}^{n/2} P(MC(G_{i}) = MC(G_{i - 1})) \\
	&\geq \left(1 - \frac{2}{n}\right) \left(1 - \frac{2}{n - 1}\right)\left(1 - \frac{2}{n - 2}\right) \cdots \left(1 - \frac{2}{n/2}\right) \\
	&= \frac{n-2}{n} \frac{n-3}{n-1}\frac{n-4}{n-3} \cdots \frac{n/2}{n/2 + 2}\frac{n/2-1}{n/2 + 1}\frac{n/2 - 2}{n/2} \\
	&= \frac{(n/2-2)(n/2-1)}{n(n-1)} \\
	&\approx \frac{1}{4}.
\end{align*}
$$
This is much better than the previous bound for the full graph.
### 4-Contract
In the previous section, we found that the probability of preserving the minimum cut after $n/2$ contractions is $\approx 1/4$. Here, we use this fact to derive a general purpose algorithm for Karger's min-cut. The algorithm for 4-contract is as follows:
- $G_0 = (V_0, E_0)$ is the initial un-contracted graph
- while $|V_0| > 2$
	- for $i = 1$ to $4$
		- run the random edge contraction algorithm on $G_0$ for $n/2$ iterations to get a graph $G_i = (V_i, E_i)$
	- 4-contract($G_1$)
	- 4-contract($G_2$)
	- 4-contract($G_3$)
	- 4-contract($G_4$)
Where we return the smallest capacity found. The runtime here is $T(n) = 4T(n/2) + O(n^2)$ where by the master theorem we get $T(n) = O(n^2 \log n)$.

Again, we can run an analysis of the probability we find the minimal cut in $G$. Here,
$$
\begin{align*}
	&P(\text{success for a graph of size }n) = 1 - P(\text{failure on all 4 branches}) \\
	&= 1 - P(\text{failure on a branch})^4 \\
	&= 1 - (1 - P(\text{success on a branch}))^4 \\
	&= 1 - \left(1 - \frac{1}{4}P(\text{success on a branch for a graph of size } n/2)\right)^4 \\
	p(n) &= 1 - \left(1 - \frac{1}{4} p\left(\frac{n}{2}\right)\right)^4 \\
\end{align*}
$$
where we substitute the probability for success on a graph of size $n$ with $p(n)$. Running an analysis on this,
$$
\begin{align*}
	p(n) &= 1 - \left(1 - \frac{1}{4} p\left(\frac{n}{2}\right)\right)^4 \\
	&= p\left(\frac{n}{2}\right) - \frac{3}{8} p\left(\frac{n}{2}\right)^2 + \frac{1}{16} p\left(\frac{n}{2}\right)^3 - \frac{1}{256} p\left(\frac{n}{2}\right)^4 \\
	&> p\left(\frac{n}{2}\right) - \frac{3}{8} p\left(\frac{n}{2}\right)^2
\end{align*}
$$
we can then use induction to prove that if $p(n/2) > \frac{1}{\log(n/2)}$, then $p(n) > \frac{1}{\log(n)}$. The proof here is weird so I've omitted it. 

So, if we run 4-contract $(\log n)^2$ times, we get that the probability of success is,
$$
\pi = 1 - \left(1 - \frac{1}{\log n}\right)^{(\log n)^2}
$$
for sufficiently large $n$, we have $\pi \approx 1 - e^{-\log n} = 1 - 1/n$, which means that we are almost certain that we retrieve the solution for large $n$.