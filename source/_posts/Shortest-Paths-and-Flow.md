---
title: Shortest Paths and Flow
date: 2018-12-15 21:18:00
categories: Algorithm and Graph
tags: [searching, graph, algorithm]

---

| Algorithm                                  | Comments                           | Graph Rep  | Running time   | Space Used |
| ------------------------------------------ | ---------------------------------- | ---------- | -------------- | ---------- |
| [Bellman-Ford](#bellman-ford-algorithm)    | Single-Source                      | Adj List   | 𝑂(𝑉𝐸)          | 𝑂(𝑉)       |
| [In DAG Out DAG](#shortest-paths-in-a-dag) | Single-Source DAG                  | Adj List   | 𝑂(𝑉+𝐸)         | 𝑂(𝑉)       |
| [Dijkstra](#dijkstra-algorithm)            | Single-Source<br />Non-Neg Weights | Adj List   | 𝑂((𝑉 + 𝐸)log𝑉) | 𝑂(𝑉)       |
| [All-pairs 1](#first-dp-formulation)       | All-Pairs                          | Adj Matrix | 𝑂(𝑉^4^)        | 𝑂(𝑉^2^)    |
| [All-pairs 2](#second-dp-formulation)      | All-Pairs                          | Adj Matrix | 𝑂(𝑉^3^log𝑉)    | 𝑂(𝑉^2^)    |
| [Floyd-Warshall](#floyd-warshall)          | All-Pairs                          | Adj Matrix | 𝑂(𝑉^3^)        | 𝑂(𝑉^2^)    |



# Shortest Paths

>  All algorithms, except **Dijkstra**, allow **negative weights**, 
> but there can *NOT* be **negative cycle**. 

**Shortest path problem:** Find the shortest path from `s` to `t`.

**Single-source shortest path:** Find the shortest path from `s` to every node.

<div id="cut-and-paste-argument"> </div>

**Lemma (Cut and Paste Argument):** 

> Let 𝑃 = (𝑠, … , 𝑢, ... , 𝑡) be the shortest 𝑠 − 𝑡 path.  The n the subpaths 𝑃~1~ = (𝑠, … , 𝑢) and 𝑃2= (𝑢, . . 𝑡) must also be, respectively, shortest 𝑠 − 𝑢 and 𝑢 − 𝑡 paths.

**Relaxation:**

> Let v.d be the shortest distance found so far from the starting node s to node v, and v.p be the last node in the current shortest path from s to node v.
>
> ![1544956501161](/assets/Shortest-Paths-and-Flow/1544956501161.png)

## Single Source Shortest Path

Let `ss(s,v)` store shortest path distance from `s` to `v`

### Bellman-Ford Algorithm

Initially, set `n.d` = inf for all nodes. Except starting node `s.d` = 0
Relax all edges once ... path length maximum 1
Relax all edges second time ... path length maximum 2

A path may have  at most `V-1` edges. So we it `V-1` times.

Finally, `n.d` is the **actual** shortest distance from `s` to `n`

#### Basic Implementation

```pseudocode
Shortest-Path(G,s):
for each node n in V do
	n.d = inf
s.d = 0
for i = 1 to V-1
	for each edge (u,n) in E
		Relax(u,n)
```

Running time: Θ(𝑉𝐸)
Space: Θ(𝑉)

#### Efficient Implementation

Take advantage of dynamic programming.

**Recurrence:**
Suppose `(u,n)` is the last edge of shortest path of length at most `i` from `s` to `n`. By [cut and paste argument](#cut-and-paste-argument), the subpath from `s` to `u` must be a shortest path of length at most i - 1, followed by `(u,n)`.
So, for all `i` > 0 and `n` != `s`

> n.d[i] = min{ u.d[i-1] + w(u,n) } 	u in V, (u,n) in E

Final solution: `n.d[m-1]`

```pseudocode
Bellman-Ford(G,s):
for each node n in V
	n.d = inf, n.p = nil
s.d = 0
for i = 1 to V-1
	for each node u in V
		if u.d changed in previous iteration then 
			for each un in Adj(u)
				Relax(u,un)
	if no un.d changed in this iteration => terminate
```

Running time: Θ(𝑉𝐸)
Space: Θ(𝑉)

### Shortest Paths in a DAG

By subpath optimality, we have

> ss(s,n) = min { ss(s,u) + w(u,n)  } u in V, (u,v) in Es

Unlike [Bellman-Ford](#bellman-ford-algorithm), each edge will only be relaxed once.

We need to ensure that when  `n` is processed, ALL `u` with `(u,n)` in `E` have already been processed. We can do that by processing `n` (also `ss(s,n)`) in the [topological](../Graph-Basic-and-Algorithm/#topological-sort) order of the nodes.

```pseudocode
DAG-Shortest-Path(G,s)
topologically sort the vertices of G
for each vertex n in V
	n.d = inf
	n.p = nil
s.d = 0
for each vertex u in topological order 
	for each vertex un in Adj(u)
		Relax(u,un)
```

Running time: Θ(𝑉+𝐸)

![1545052561597](/assets/Shortest-Paths-and-Flow/1545052561597.png)

### Dijkstra Algorithm

*NOT* allow **negative** weights.

Maintain a set of **explored nodes** `S` for which we know `u.d = ss(s,u)`by variable `status` 
 Initialize `S = {s}`, ` s.d = 0`, ` v.d = inf`

Use a **Min priority queue**`Q` on `V` with `d` as key

#### Key Lemma

> If all edges leaving `S` are relaxed, then `v.d = ss(s,v) `,where `v` is the vertex in `V-S` with the minimum `v.d`
>
> So this `v` can be added to `S`, then repeat

#### pseudocode

```pseudocode
Dijkstra(G,s):
for each node n in V do
	n.d = inf, n.p = nil, n.status = unknown
s.d = 0
create a min priority queue Q onV with d as key
while Q not empty	% E times
	u = Extract-Min(Q) % O(logV) time
	u.status = over
	for each un in Adj(u) do
		if un.status = unknown then
			Relax(u,un)
			Decrease-Key(Q,un,un.d)
```

Running time: 𝑂(𝐸log𝑉)
Very similar to [Prim's algorithm](../Minimum-Spanning-Tree/#prims-algorithm)

![1545052597485](/assets/Shortest-Paths-and-Flow/1545052597485.png)

## All-Pairs Shortest Paths

**Input**:

- Directed graph `G = (V,E)`
- Weight `w(e)` = length of edge `e`

**Output:**

- `ss(u,v)`, for all pairs of nodes `u`, `v`
- A data structure from which the shortest path from `𝑢` to `𝑣` can be extracted efficiently, for any pair of nodes `𝑢`, `𝑣`
  - Note: Storing all shortest paths explicitly for all pairs requires 𝑂(𝑉^3^) space.

**Graph Representation:**

- Assume adjacency matrix:
  - `𝑤(𝑢, 𝑣)` can be extracted in 𝑂(1) time.
  - `𝑤(𝑢, 𝑢)= 0`, `𝑤(𝑢, 𝑣)= ∞ `if there is no edge from `𝑢` to `𝑣`.
- If the graph is stored in adjacency lists format, can convert to adjacency matrix in 𝑂(𝑉2) time.

### Using Previous Algorithms

NOT negative cost edges: [Dijkstra's algorithm](#dijkstra-algorithm)
Running time: 𝑂(𝐸 log 𝑉), totally 𝑂(𝑉𝐸 log 𝑉)
Space: 𝑂 (𝑛^3^ log 𝑛 ) for [dense graphs](https://en.wikipedia.org/wiki/Dense_graph)

HAVE negative cost edges: [Bellman-Ford](#bellman-ford-algorithm)
Running time: 𝑂(𝑉𝐸), totally 𝑂(𝑉^2^𝐸)
Space: 𝑂 (𝑛^4^) for [dense graphs](https://en.wikipedia.org/wiki/Dense_graph)

---

### First DP Formulation

#### Define
 `d(i,j,m)` = length of the shortest path from `i` to `j` that **contains at most `m` edges**.
Use `D[m]` to denote the matrix `[d(i,j,m)]`
![1544967538439](/assets/Shortest-Paths-and-Flow/1544967538439.png)

#### Recurrence( essentially the same as in [Bellman-Ford](bellman-ford-algorithm)):

> d(i,j,m)  = min { d(i,k,m-1) + w(k,j) }	k from 1 to n
> initially d(i,j,1) = w(i,j)

#### pseudocode

```pseudocode
Slow-All-Pairs-Shortest-Paths(G):
d(i,j,1) = w(i,j) for all 1<=i,j<=n
for m = 2 to n-1
	let D[m] be a new n*n matrix
	for i = 1 to n
		for j = 1 to n
			d(i,j,m) = inf
			for l =1 to n
				if d(i,k,m-1) + w(k,j) < d(i,j,m) then
					d(i,j,m) = d(i,k,m-1) + w(k,j)
return D[n-1]

```

Running time: 𝑂(𝑛^4^)
Space: 𝑂(𝑛^3^) can be improved to 𝑂(𝑛^2^)

---

### Second DP Formulation

#### Observation

To compute `d(i,j,m)`, instead of looking at the last stop before `j`, we look at the *middle point*. This cuts down the problem size by half. Thus, to calculate `D[1]`,`D[2]`,`D[4]`,`D[8]`,...

*Note that overshooting `D[n-1]` is OK. Since `D[n']` , `n'>n -1 ` has the shortest paths with up to `n'` edges. It will not miss any shortest path with up to n-1 edges.*

#### Recurrence

> d(i,j,2s)  = min { d(i,k,s) + d(k,j,s) }	k from 1 to n
> initially d(i,j,1) = w(i,j)

#### Analyze

Running time: 𝑂(𝑛^3^) for each matrix , totally 𝑂(𝑛^3^log𝑛)

---

### Floyd-Warshall

#### Define

`d(i,j,k)` = length of the shortest path from `i` to `j` that all intermediate vertices on the path (if any) are **in the set `{1,2,...,k}`**
![1544967564935](/assets/Shortest-Paths-and-Flow/1544967564935.png)

#### Observation

When computing `d(i,j,k)` there are two cases:

1. `k` is not a node on the shortest path from `i` to `j`
   => then the path uses only vertices in `{1,2,...,k-1}`
2. `k` is an intermediate node on the shortest path from `i` to `j`
   => path can be spilt into shortest subpath from `i` to `k` and a subpath from `k` to `j`
   Both subpaths use only vertices in `{1,2,...,k-1}`

#### Recurrence

![1544967760217](/assets/Shortest-Paths-and-Flow/1544967760217.png)

#### pseudocode

```pseudocode
Floyd-Warshall(G):
d(i,j,0) = w(i,j) for all 1<=i,j<=n
for k=1 to n
	let D[k] be a new n*n matrix
	for i = 1 to n
		for j = 1 to n
			if d(i,k,k-1) + d(k,j,k-1) < d(i,j,k-1) then
				d(i,j,k) = d(i,k,k-1) + d(k,j,k-1)
			else 
				d(i,j,k) = d(i,j,k-1)
return D[n]
```

Running time: 𝑂(𝑛^3^)
Space: 𝑂(𝑛^3^) but can be improved to 𝑂(𝑛^2^)
Surprising discovery: If we just drop all third dimension. i.e. the algorithm just uses n*n array `D`, the algorithm still works!

---

# Maximum Flow

**Input:** A directed connected graph `𝐺 =(𝑉, 𝐸)` , where

- every edge `𝑒 ∈ 𝐸` has a **capacity** `𝑐(𝑒)`;
- a source vertex `𝑠` and a target vertex `𝑡`.

**Output:** A **flow** `𝑓: 𝐸 → 𝐑` from `𝑠` to `𝑡`, such that

- For each `𝑒 ∈ 𝐸, 0 ≤ 𝑓(𝑒) ≤ 𝑐(𝑒)` (capacity)
- For each `𝒗 ∈ 𝑽 − {𝒔, 𝒕}`, `sumOut( 𝒇(𝒆) ) = sumInto( 𝒇(𝒆) ) `(conservation)、

**Define:**
The value of a flow is ` |𝑓| = sum(𝑓(𝑠, 𝑣))= sum(𝑓(𝑣, 𝑡))` where `𝑣` in `V`

## s-t Cut

![1544969825169](/assets/Shortest-Paths-and-Flow/1544969825169.png)

## Residual Graph

![1544970376338](/assets/Shortest-Paths-and-Flow/1544970376338.png)

## Ford Fulkerson Algorithm

1. Start with `f(e) = 0` for all edges `e` in `E`
2. Construct Residual Graph G~f~ for current flow `f(e) = 0`
3. while there exists some s-t path `P` in G~f~
4. Let capacity of flow `cf(p)` = min { `cf(e)`: `e` in `P`}
   This is the maximum amount of flow that can be pushed through residual capacity of `P`'s edges
5. Push `c(f,p)` units of flow along the edges `e` in `P` by adding `cf(p)` to `f(e)` for every `e` in `P`
6. Construct Residual Graph G~f~ for new current flow `f(e)`

*When algorithm gets stuck, current flow is maximal!*

```pseudocode
Ford-Fulkerson(G,s,t):
for each (u,n) in E do
	f(u,n) = 0
	cf(u,n) = c(e)
	cf(n,u) = 0
while there exists path P in residual graph Gf do
	cf(p) = min {cf(e):e in P}
	for each edge (u,n) in P do
		if (u,n) in E then
			f(u,n) = f(u,n) + cf(p)
			cf(u,n) = cf(u,n) - cf(p)
			cf(n,u) = cf(n,u) + cf(p)
		else
			f(n,u) = f(n,u) - cf(p)
			cf(n,u) = cf(n,u) + cf(p)
			cf(u,n) = cf(u,n) - cf(p)
```

![1544971779763](/assets/Shortest-Paths-and-Flow/1544971779763.png)

Construct Residual Graph **G~f~**

![1544971840720](/assets/Shortest-Paths-and-Flow/1544971840720.png)

capacity of flow in 8, write back to **G**

![1544971890428](/assets/Shortest-Paths-and-Flow/1544971890428.png)

The **G~f~** in next iteration is

![1544971927243](/assets/Shortest-Paths-and-Flow/1544971927243.png)

Until there is **no s-t path in G~f~.** Current flow is optimally maximal.

## Applications

he Max Flow setup can model (surprisingly) many (seemingly) unrelated problems.  

The idea is to express the problem as a max flow and then feed individual instances into out max flow solver.

The examples below all share the property that they are integer flow problems, e.g., al capacities are integral, so running-time analyses can use FF bound for integral flows.

### Edge-Disjoint Paths

> **Define:** Two paths are edge-disjoint if they have no edge in common.

![1544972200654](/assets/Shortest-Paths-and-Flow/1544972200654.png)

### Circulations with Demands

> Given a number of source vertices 𝑠1, 𝑠2, …, each with a supply of 𝑠𝑢𝑝(𝑠𝑖) 
> and a number of target vertices 𝑡1, 𝑡2, …, each with a demand of 𝑑𝑒𝑚 𝑡𝑖 ;
> sum of supply >= sum of demand
>
> Need a flow meets all demands.

Solution:
Add a **super source** and a **super target**

### Maximum Bipartite Matching

> A Matching is a subset M ⊆ E such that:
> ∀v ∈ V at most one edge in M is incident upon v.
>
> The Size |M| is the number of edges in M.
>
> A Maximum Matching is matching M such that:
> every other matching Mʹ satisfies |Mʹ | ≤ M.

> Given bipartite graph G, find a Maximum Matching.

Formulation:

- Create directed graph 𝐺′ = (𝑋 ∪ 𝑌 ∪ {𝑠, 𝑡}, 𝐸′ ).
- Direct all edges from 𝑋 to 𝑌, and assign them capacity 1.
- Add source 𝑠, and unit capacity edges from 𝑠 to each node in 𝑋.
- Add target 𝑡, and unit capacity edges from each node in 𝑌 to 𝑡.

Theorem: Max cardinality matching in 𝐺 = value of max flow in 𝐺′.

![1544973114900](/assets/Shortest-Paths-and-Flow/1544973114900.png)

Running time: 𝑂(𝑉𝐸)

### Baseball Elimination

![1544973389780](/assets/Shortest-Paths-and-Flow/1544973389780.png)

**Input：**

- 𝑛 teams: 1, 2, … , 𝑛

- One particular team, say 𝑛 (without loss of generality)
- Team 𝑖 has won 𝑤~𝑖~ games already
- Team 𝑖 and 𝑗 still need to play 𝑟~𝑖𝑗~ games, 𝑟~𝑖𝑗~ = 0 or 1.
- Team 𝑖 has a total of 𝑟𝑖 = sum(𝑟~𝑖𝑗~: 𝑗) games to play

**Output:**

- “Yes”, if there is an outcome for each remaining game such that team 𝑛 finishes with the most wins (tie is OK).
- “No”, if no such possibilities.

![1544973553493](/assets/Shortest-Paths-and-Flow/1544973553493.png)

**Claim: There is a way for team `n` to finish in the first place iff the max flow has value of the sum of supply from source `s`**