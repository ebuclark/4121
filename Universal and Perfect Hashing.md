We start with a scenario, where you are given an assignment to implement a hashing algorithm. You are graded in pairs, through testing your partner's hash function. However, your partner is smart:
- He analyses the hash function
- Picks a sequence of worst-case keys to degrade search accuracy to $O(n)$ in a resident hashmap

How do we fix this? By randomising the hashing. We can pick a hash function independent to the keys which are stored. This guarantees good performance on average over many runs.

## Universal Hashing
Let $H$ be a finite collection of hash functions that map a given universe $U$ of keys into a range $\{1, \ldots, m - 1\}$. We say $H$ is **universal** if for each pair of distinct keys $x, y \in U$, the number of hash functions $h \in H$ for which $h(x) = h(y)$ is $|H|/m$. This means that if we take two different keys and randomly pick a hash function, the chance of collision for two keys is $1/m$.

Assume a family of hash function $H$ is universal and let $x, y \in U$ be arbitrary keys. For a randomly chosen $h \in H$ the variable $c_{xy} = 1$ if the two keys collide. If we fix $x$, then
$$
\begin{align*}
	\mathbb{E}[c_{xy}] &= P(h(y) = h(x)) \times 1 + P(h(y) \neq h(x)) \times 0 \\
	&= P(h(y) = h(x)) \\
	&= \frac{1}{m}.
\end{align*}
$$
Again, assume $H$ is a universal family of hash functions and we are hashing $n$ keys into $m$ buckets. Let $C_{x}$ be the total number of collisions involving a key $x$,
$$
	C_x = \sum_{y = 1, y \neq x}^n c_{xy},
$$
then, the expected value is,
$$
	\mathbb{E}[C_x] = \sum_{y = 1, y \neq x}^n \mathbb{E}[c_{xy}] = \frac{n-1}{m}.
$$
So if $n \leq m$ the number of collisions for a certain key is less than 1.

### Designing Universal Hash Functions
Choose the size of the hash table $m$ to be a prime number. Let $r$ satisfy $m^r \leq |U| < m^{r+1}$ where $U$ is the universe of all keys - $r = \log_m |U|$. Represent each key in base $m$,
$$
\begin{align*}
	x = \sum_{i=0}^r x_i m^i
\end{align*}
$$
for a vector $x \in \mathbb{R}^r$. So, we pick a random non-zero vector $a \in \mathbb{R}^r$ to represent the hash function,
$$
h_a(x) = \left(\sum_{i=0}^r x_i a_i\right) \mod m
$$
where we include the modulo to ensure we can insert into the $m$ buckets.

Now, let us analyse the chance for collision here. Say we have two vectors $x$ and $y$ which we wish to hash. If their hashes are equal we have,
$$
\begin{align*}
	\left(\sum_{i=0}^r x_i a_i\right) &= \left(\sum_{i=0}^r y_i a_i\right) \mod m \\
	\left(\sum_{i=0}^r x_i a^i - y_i a_i\right) &= 0  \mod m\\
	\left(\sum_{i=0}^r (x_i - y_i) a_i\right) &= 0 \mod m.
\end{align*}
$$
Say that only the first entries are equal, that means
$$
\begin{align*}
	-\left(\sum_{i=1}^r (x_i - y_i) a_i\right) &= (x_0 - y_0)a_0 \mod m \\
	a_0 &= -\left(\sum_{i=1}^r (x_i - y_i) a_i\right) (x_0 - y_0)^{-1} \mod m \\
\end{align*}
$$
since $m$ is prime, this implies that there is exactly one $a_0$ for which this occurs. Therefore, the chance of collision here is calculated by the total number of sequences we can pick for $a$ divided by the total number of vectors that include $a_0$ as the first entry. This is 
$$
\frac{m^{r}}{m^{r+1}} = \frac{1}{m}
$$
and we therefore have a universal family of hash functions.

## Random Hash Table
- Given $n$ keys we construct tables of size $m < 2n^2$ using universal hashing.
- Claim: probability that such a table is collision free will be $> 1/2$.

The way we design the table is simple,
- Pick the least prime $m$ such that $m > n^2$ and $m < 2n^2$.
- Generate a random vector $a$ and hash all keys with a function $h_a$. Note this hash function is universal.

If we have $n$ keys there will be ${n \choose 2}$ pairs of keys. Then, we have 
$$
	{n \choose 2} \frac{1}{m} \leq \frac{n(n-1)}{2n^2}
$$
collisions (just expanding the choose coefficients and using the inequality $m < 2n^2$). This is therefore $< 1/2$ as we can artificially cancel the numerator and denominator. If $X$ is the random variable equal to the number of collision in the table, we have 
$$
	P(X \geq 1) \leq \frac{\mathbb{E}[X]}{1} < \frac{1}{2}
$$
the probability of one collision being bound by $1/2$. For $k$ collisions, we obviously have a much smaller bound of $(1/2)^k$.

The rest is on hashing in a secondary table, look at the actual slides.