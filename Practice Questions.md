## Question 1
We have a $1/4$ chance of seeing a car after a truck and therefore $3/4$ chance of seeing a truck after a truck. A $1/5$ chance of seeing a truck after a car, so a $4/5$ chance of seeing a car after a car.

This makes a transition matrix
$$
\begin{bmatrix}
	1/4 & 3/4 \\
	1/5 & 4/5 \\
\end{bmatrix}
$$
where the first column and row are TRUCK and second column and row are CAR. Then, we try to solve for the stationary distribution for fractions $t$ for truck and $c$ for car,
$$
(t, c) \begin{bmatrix} 
	1/4 & 3/4 \\
	1/5 & 4/5 \\
\end{bmatrix} = (t, c)
$$
Which gives equations,
$$
\begin{align*}
	1/4 t + 1/5c = t \\
	3/4 t + 4/5c = c
\end{align*}
$$
We also know that $t + c = 1$ by definition, so
$$
\begin{align*}
	1/4 t + 1/5(1 - t) &= t \\
	1/20t + 1/5 &= t \\
	t &= 4/19 \text{ and } c=15/19 \\
\end{align*}
$$
## Question 2
I don't really get this.
## Question 3
Say that a vertex $v$ has a periodicity of $k$ in the strongly connected graph. Pick a vertex $u$ which immediately precedes it in one of the loops (i.e $u$ is an adjacent vertex). Assume that $u$ has another outgoing edge to a vertex which $v$ is not adjacent to, causing a shorter loop.  This shorter loop, however, is also divisible by $k$, since we again have one edge for a loop containing $u$. We can apply this logic to all vertices in all paths to show that all vertices are periodic in $k$.
## Question 4
### Part A
Let $X$ be a random variable representing the number of coins we win in this game. The probability we at least $3/4 n$ coins in total is,
$$
\begin{align*}
	P(X \geq 3/4n) \leq \frac{\mathbb{E}[X]}{3/4n}
\end{align*}
$$
Where the expected value is
$$
\begin{align*}
	\mathbb{E}[X] &= \sum_{i=1}^n 1/2  = n/2
\end{align*}
$$
Therefore,
$$
\begin{align*}
	P(X \geq 3/4n) &\leq \frac{n/2}{3/4n} \\
 &= \frac{2}{3}
\end{align*}
$$
### Part B
We can simply use the compliment of the previous result. Since the coin is fair, the probability you win $3/4n$ is the same as the probability you lose $3/4n$, therefore they are both bound by $2/3$.

### Part C
The probability to win $k$ out of $n$ coins is simply,
$$
P(X = k) = {n\choose k} (1/2)^k (1/2)^{n-k}
$$
So  the probability to win at least $k$ is
$$
P(X \geq k) = \sum_{i = 1}^k {n\choose i} (1/2)^i (1/2)^{n-i}
$$
## Question 5
### Part A
If we split elements into groups of $7$, we have $(n/7)/2 = n/14$ elements less than the pivot (if the pivot is the median of the entire grid). This means that we also have $4n/14$ elements less than and greater than the pivot. 

So the runtime calculation becomes
$$
T(n) \leq T(n/7) + T(10n/14) + Cn \\
$$
If we choose $T(n) < KCn$, we get the runtime as
$$
\begin{align*}
	T(n) &\leq T(n/7) + T(10n/14) + Cn \\
	&= n/7 \times KC + 10n/14 \times KC + Cn \\
	&= 6n/7 \times KC + Cn \\
\end{align*}
$$
Therefore, 
$$
\begin{align*}
	KCn \leq 6n/7 \times KC + Cn\\
	K \leq 6/7 \times K + 1\\
	K \geq 7
\end{align*}
$$
and we choose $K = 7$ for a runtime $T(n) \leq 6nC + Cn = 7Cn$.

### Part B
If we choose to split into groups of three, we have $n/6$ elements in total less than the pivot where there are $n/3$ elements less than and greater than the pivot. If we define the runtime as $T(n) < KCn$ for some $K \geq 1$, we get
$$
\begin{align*}
	T(n) &\leq T(n/3) + T(2n/3) + Cn \\
	 &= 1/3 \times KCn + 2n/3 \times KCn + Cn \\
	 &= KCn + Cn \\
\end{align*}
$$
Which implies that,
$$
\begin{align*}
	 KCn \leq KCn + Cn \\
\end{align*}
$$
However, as $C \geq 1$ and $n \geq 1$, this inequality cannot hold, and therefore there is no valid $K$.
## Question 6
To find the $k$th smallest element in $\log n$ time, we can store the number of elements each skip connection jumps. This means that we track how big the skips we take are, and only take the largest possible step which is less than a total of $k$ when traversing the list. This will inherently allow us to take at most $O(\log n)$ total skips due to the random generation of the skip connections. 

This solution does however have the drawback of requiring the update of these indexes every time an element is inserted and removed, making insertion and deletion both worst case $O(n)$.
## Question 7
The probability that the MinCut algorithm finds a solution is at least $1/\log n$. So if we repeat this $k (\log n)^2$ times, we get,
$$
\begin{align*}
	\pi &= 1 - \left(1 - \frac{1}{\log n}\right)^{k (\log n)^2} \\
	&= 1 - \left(\left(1 - \frac{1}{\log n}\right)^{(\log n)}\right)^{k(\log n)} \\
	&\approx 1 - e^{-k \log n} \\
	&= 1 - \frac{1}{n^k} \\
\end{align*}
$$
and we therefore run the algorithm for $k = 2$ or $2(\log n)^2$ times to ensure a probability of success at $1 - 1/n^2$. The asymptotic runtime itself won't change, just the number of times we run the algorithm.
## Question 8
### Part A
We can redesign PageRank to use a ratio as follows,
$$
	\rho(P_i) = \sum_{P_j \in C(P_i)} \frac{\rho(P_j)}{|C(P_j)|}
$$
where $C(P_i)$ is the set of citations for a paper $P_i$. This means that a paper is ranked highly if it is cited by papers which cite lower numbers of papers, avoiding the case of papers appearing highly ranked if they are cited by surveys or metanalyses. Again, we use the idea of dangling papers and a randomised approach (where we randomly jump from paper to paper) to estimate these values.
### Part B
In this case, we can use the number of times an author is cited as a weighted edge. Since we can assume most citations are genuine, an author being cited by another frequently indicates that their work is important. Using this as the $\rho(\mathord{\cdot})$ value for a researcher, we can then apply the PageRank style random traversal algorithm to jump around different 'groups' of researchers, where we typically will not have a 'dangling' researcher who has never cited another author.
## Question 9
This is just question 1 lol.
## Question 10
Here we have pleasant weather days as $P$,  summer shower as $S$ and hot day as $H$.
- $P \to P$ is $7/10$
- $P \to S$ is $1/10$
- $S \to S$ is $2/15$
- $H \to P$ is $4/9$
- Probability of $P$ is $5/9$
- Probability of $S$ is $1/9$
- Probability of $H$ is $1/3$

We need to calculate what fraction of days with a shower are followed by a hot day. First, we draw the transition matrix as follows,

| | P | S | H |
| --- | --- | ---| --- |
| P | 7/10 | 1/10 | 2/10 |
| S | --- | 2/15 | --- |
| H | 4/9 | ---| --- |

Then, we have an equation to find the stationary distribution as,
$$
(5/9, 1/9, 1/3) \begin{bmatrix}
7/10 & 1/10 & 2/10 \\
s_1 & 2/15 & s_2 \\
4/9 & h_2 & h_3
\end{bmatrix} = (p, s, h) 
$$
Then,
$$
\begin{align*}
	(p, s, h) &= (29/54 + s1/9, 19/270 + h_2/3, 1/9 + s_2/9 + h_3/3) \\
	p &= 29/54 + s1/9 \\
	s &= 19/270 + h_2/3 \\
	h &=  1/9 + s_2/9 + h_3/3
\end{align*}
$$
where we know that $s_2 = 13/15 - s_1$ and $h_3 = 5/9 - h_2$.

## Question 11

If we have $y = x^T M$, the sum of the entries of $y$ is,
$$
\begin{align*}
	\sum_{i=1}^n y_i &= \sum_{i=1}^n\sum_{j=1}^n x_j s_{ij} \\
	&= \sum_{j=1}^n\sum_{i=1}^n x_j s_{ij} & \text{ switching the order of summation} \\
	&= \sum_{j=1}^n x_j \sum_{i=1}^n s_{ij} \\
	&= 1 
\end{align*}
$$

## Question 12
### Part A
The basic matrix $G_1$ (without random jumps) will look something like
$$
G_1 = \begin{pmatrix}
	0 & 1/4 & 1/4 & 1/4 & 0 & 0 & 0 & 1/4 \\
	1/4 & 0 & 1/4 & 0 & 1/4 & 0 & 1/4 & 0 \\
	0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 \\
	0 & 0 & 0 & 0 & 1/2 & 1/2 & 0 & 0 \\
	0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
	1/2 & 0 & 0 & 0 & 0 & 0 & 1/2 & 0 \\
	0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
	1/3 & 0 & 1/3 & 0 & 1/3 & 0 & 0 & 0 \\
	\end{pmatrix}
$$
As we have $\alpha = 7/8$ and $M = 8$, we can now add these factors into every term to get the final matrix $G$. That is, we multiply each existing value by $\alpha$ and add $(1-\alpha)/M$. If a vertex has no outgoing edges, we simply replace its values by $1/M$ or $1/8$.
$$
G = \begin{pmatrix}
	1/64 & 15/64 & 15/64 & 15/64 & 1/64 & 1/64 & 1/64 & 15/64 \\
	15/64 & 1/64 & 15/64 & 1/64 & 15/64 & 1/64 & 15/64 & 1/64 \\
	1/64 & 1/64 & 1/64 & 57/64 & 1/64 & 1/64 & 1/64 & 1/64 \\
	1/64 & 1/64 & 1/64 & 1/64 & 29/64 & 29/64 & 1/64 & 1/64 \\
	1/8 & 1/8 & 1/8 & 1/8 & 1/8 & 1/8 & 1/8 & 1/8 \\
	29/64 & 1/64 & 1/64 & 1/64 & 1/64 & 1/64 & 29/64 & 1/64 \\
	1/8 & 1/8 & 1/8 & 1/8 & 1/8 & 1/8 & 1/8 & 1/8 \\
	59/192 & 1/64 & 59/192 & 1/64 & 59/192 & 1/64 & 1/64 & 1/64 \\
	\end{pmatrix}
$$
### Part B
The PageRank matrix is row stochastic, meaning its rows sum to $1$ and it therefore can be used as a Markov chain transition matrix.
### Part C
Brother, just use an LP solver or something.
## Question 13
If a Markov chain is irreducible, this means we have a strongly connected graph, where we can reach any node from any other node. If it is aperiodic, this means that there is no 'pattern' or exact length of each loop within the graph. As the Markov chain is irreducible, this means that we can make a loop of arbitrary length, and when it is aperioidic, no loop can be divisible by length $k$.

## Question 14
One way to have numbers which are close to each other have more similar hash values is simply to reduce $p$ so that the hash does not produce a larger range of values, leading to the hashing results being closer to each other. This obviously creates more hashing conflicts and makes the result less desirable.

Another way to do this is to represent the numbers in base $m$ where $m$ is a prime number (typically greater than $n^2$ where $n$ is the number of keys). Then, we generate a random vector $\mathbf{a} \in \mathbb{R}^r$ with non-zero entries less than or equal to $m$ and for $r = \log_m n$. The hash function is then
$$
	h_a(x) = \sum_{i=1}^r x_i \mathbf{a}_i \mod m
$$

where $x_i$ is the individual 'bit' in the base $m$ representation of $x$. This randomised approach means that numbers with similar base $m$ representations (i.e numbers close to each other) also have similar hash results.

## Question 19
We are given a hash function 
$$
h_{a, b}(x) = \left(\sum_{0 \leq i \leq r} x_i a_i \mod m, \sum_{0 \leq i \leq r} x_i b_i \mod m\right)
$$
and told to prove it is universal.

If we have a collision between two keys $x$ and $y$, 

$$
\begin{align*}
	\sum_{0 \leq i \leq r} x_i a_i &= \sum_{0 \leq i \leq r} y_i a_i \mod m  \\
	\sum_{0 \leq i \leq r} (x_i - y_i)a_i &= 0 \mod m \\
\end{align*}
$$
then, if the first two entries are equal,
$$
\begin{align*}
	a_0(x_0 - y_0) &= -\sum_{1 \leq i \leq r} (x_i - y_i)a_i  \mod m \\
	a_0 &= -\sum_{1 \leq i \leq r} (x_i - y_i)a_i (x_0 - y_0)^{-1} \mod m \\
\end{align*}
$$
as $m$ is prime there is only one $a_0$ where this is the case. Since we can choose $r + 1$ digits for vector $a$, there are a total number of $m^{r+1}$ possible $a$ vectors. If the first entry causes this collision, then we can choose the remaining $r$ entries for a total number of $m^r$ cases where an entry causes a collision. Therefore, we have the probability of collision at
$$
\frac{m^r}{m^{r+1}} = \frac{1}{m}.
$$

Without loss of generality, the same logic applies for some $b_0$, where we have $1/m$ chance of collision. Now, $a_0 = b_0$ since the two hash functions are identical and $m$ is prime. Therefore, we have $1/m$ chance of selecting equal $a_0$ and $b_0$. This means that we only account for the probability of collision once, since we do not need to double up the same probability. So, the total probability of collision for the combined hash functions is 
$$
\frac{1}{m} \times \frac{1}{m} = \frac{1}{m^2}
$$
and we therefore have a universal hash function.