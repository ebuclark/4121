# Center-based clustering

Clustering  algorithms based around centers based on some distance metric. E.g. k-means clustering finds a partition $C = {C_1,\dots,C_k}$ for a set of data points $A = {\mathbf{a_1},\dots,\mathbf{a_k}}$ into $k$ clusters with respective centers $\mathbf{c_1},\dots,\mathbf{c_k}$.

The goal is to minimise
$$
\Phi(C) = \sum_{i=1}^k \sum_{a\in C_j} d(\mathbf{a,c_j})^2.
$$
Which has nice properties such as if $d(\cdot,\cdot)$ is the Euclidian distance metric, then $\mathbf{c_j}$ are the centroids of the points in their clusters.

However, finding the optimal k-means clustering is an NP hard problem which cannot be solved in polynomial time. We can use approximate algorithms, e.g. Lloyd's Clustering Algorithm which starts with some initial set of centers, and iteratively replacing centers with centroids of intermediate clusters. This is an iterative procedure until some convergence criteria is met. 
Initial clusters can be chosen in a variety of ways, one of the better ones being starting each center far away from one another to better ensure convergence of centers.

When clusters are not center based, we need to apply manifold learning techniques, in this case, *spectral clustering*.

# Spectral clustering
define the *degree matrix* D as the diagonal matrix of degree $d_i$ where
$$
d_i = \sum_{j=1}^n w_{ij}
$$
and $w_{ij} \geq 0$ is the weight of an edge $e=(v_i,v_j)$ which represents some measure of similarity between two data points $\mathbf{a_i} , \mathbf{a_j}$ .
Given a subset of vertices $S \subset V$, we denote $\mathbb{I}_S$ as an n-dimensional indicator vector indicating if vertex $v_1,\dots,v_n$ belongs to the set $S$.

One typical measure of similarity is gaussian similarity, where
$$
w_{ij} = \exp{-\frac{||\mathbf{a_i}-\mathbf{a_j}||^2}{2\sigma^2}}
$$
where $\sigma$ is a scale parameter.

To perform spectral clustering, we obtain the (non-normalised) Laplacian matrix $L$ where
$$
L = D - W
$$
and $W = (w_{ij})_{i,j=1}^n$.
$L$ is a positive semi-definite matrix. 
### For a fully connected graph:
The smallest eigenvalue of $L$ is 0 with corresponding eigenvector $\mathbb{1}$.

### For k-connected components:
Suppose $G$ is now an undirected (non-negative) weighted graph with n vertices and $k$ connected components $A_1,\dots,A_k$. Then the algebraic and geometric multiplicities of eigenvalue 0 of $L$ are both equal to $k$ and the eigenspace of eigenvalue 0 is spanned by the indicator vectors $\mathbb{I}_{A_1}, \dots \mathbb{I}_{A_k}$ of these components.
Software that computes the eigenvectors and eigenvalues of $L$ will typically find a set of linear combinations of these indicator vectors.

TODO: Approximately connected components