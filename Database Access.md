We have a general problem setting where $n$ separate processes can concurrently access a database. To avoid race conditions, we use a lock to ensure no two processes can access the database at the same time. Also, assume that time is discrete $t = 1, 2, 3, \ldots$ for a more feasible analysis.

An approach to reduce the number of conflicts is to grant processes access with probability $p$ at each time $t$. That is, the processes flip a coin where their probability of accessing the database is $p$ (and conversely not accessing the data base is $1 - p$). The question now becomes, how do we pick a $p$ reduces the total number of collisions?

The probability of a process $i$ accessing the database without conflict at a time $t$ is
$$
	P(A_i) = (1 - p)^{n-1} p.
$$
That is, all other processes are not granted access, but process $i$ is. Now, our goal is to maximise this value. We start by finding the first derivative,
$$
\begin{align*}
	\frac{d P(A_i)}{dp} &= (n-1)(1 - p)^{n-2} p + (1 - p)^{n-1},
\end{align*}
$$
at a critical point,
$$
\begin{align*}
	-(n-1)(1 - p)^{n-2} p + (1 - p)^{n-1} &= 0\\
	(1-p)^{n-1}(-(n-1)(1 - p)^{-1} p + 1) &= 0\\
	-(n-1)(1 - p)^{-1} p + 1 &= 0 & \text{ as } p \neq 1\\
	(1-n) p + 1 - p &= 0 \\
	p &= \frac{1}{n}. \\
\end{align*}
$$
So, we can now substitute the optimal value $p = 1/n$ into the original equation,
$$
	P(A_i) = \left(1 - \frac{1}{n}\right)^{n-1} \frac{1}{n}.
$$
This now allows us to investigate the asymptotic behaviour of $P(A_i)$ as $n \to \infty$. We know that $(1 - 1/n)^{n} \to 1/e$ from $1/4$ (increasing) and $(1 - 1/n)^{n-1} \to 1/e$  from $1/2$ (decreasing). Therefore, 
$$
	\frac{1}{n} \cdot \frac{1}{e} \leq P(A_i) \leq \frac{1}{n} \cdot \frac{1}{2},
$$
and $P(A_i) = \Theta(\frac{1}{n})$.

We now estimate the probability of failure after $t$ timesteps (the inverse of success) to gain a measure of the effectiveness of our system. So, the probability of failure after $t$ steps is simply
$$
	P(\overline{A_i})^t = \left(1 - \left(1 - \frac{1}{n}\right)^{n-1} \frac{1}{n}\right)^t.
$$
Applying the previous inequalities we estimate
$$
	P(\overline{A_i})^t \approx \left(1 - \frac{1}{e \cdot n}\right)^t.
$$
Now, we can try different values of $t$ to investigate the probability of consecutive failures (and therefore starvation). If we try $t = e \cdot n$, we get
$$
	\left(1 - \frac{1}{e \cdot n}\right)^{(en)} \approx \frac{1}{e}
$$
which is larger than $1/3$, a quite high probability of failure. Over time however, we can choose $t = e \cdot n \cdot 2\ln n$ to get
$$
\begin{align*}
	\left(1 - \frac{1}{e \cdot n}\right)^{(e \cdot n \cdot 2\ln n)} &= \left(\left(1 - \frac{1}{e \cdot n}\right)^{e \cdot n}\right)^{2\ln n} \\
	&= \left(\frac{1}{e}\right)^{2\ln n} \\
	&= \left(\frac{1}{e}\right)^{\ln n^2} \\
	&\approx \frac{1}{n^2}.
\end{align*}
$$
Now, at $t = 2en\ln n$ the probability of one process failing is simply $n \cdot 1/n^2 = 1/n$ where the probability of all processes succeeding is $1 - \frac{1}{n}$. This time factor is only slightly larger than implementing a scheme where the clients communicate with each other to take sequential turns to access the data base, where they will all gain access by time $n$. 
