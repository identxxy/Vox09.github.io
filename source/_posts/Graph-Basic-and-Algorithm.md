---
title: Graph Basic and Algorithm
date: 2018-12-15 20:50:00
categories: Algorithm and Graph
tags: [searching, graph, algorithm]

---

## Basic

node: vertex 

edge: connection between nodes

### Undirected

- Edges have no direction (or both directions)
- deg (𝑣) = # edges at 𝑣
- sum of deg(𝑣) = 2𝐸

### Directed

- Edges have directions
- If an edge has both directions, we will use two edges with opposite directions
- deg~out~ (𝑣) = # edge leaving 𝑣;
  deg~in~ (𝑣 )  = # edge entering 𝑣.
-  sum of deg𝑜𝑢𝑡(𝑣) = sum of deg𝑖𝑛(𝑣) = 𝐸

### Path

> A **path** in a (directed or undirected) graph 𝐺 = (𝑉, 𝐸) is a sequence 𝑃 of nodes 𝑣1, 𝑣2, … , 𝑣𝑘−1, 𝑣𝑘 such that (𝑣𝑖, 𝑣𝑖+1) is an edge. The length of the path is 𝑘 − 1 (i.e., # edges in the path).
>
> A path is **simple** if all nodes are distinct.

### Connectivity

>  An *undirected* graph is **connected** if for every pair of nodes 𝑢 and 𝑣, there is a path between 𝑢 and 𝑣.
>
> Theorem: For a connected graph, 𝐸 ≥ 𝑉 − 1.

### Cycle

>  A cycle is a path v~1~, v~2~, … , v~k-1~, v~k~ in which v~1~ = v~k~ , k > 2
>
> A cycle is simple if the  first 𝑘 − 1 nodes are all distinct.

### Data structure

#### Adjacency Nodes

- Adjacency List
  - A node-indexed array of lists.
  - Given node 𝑢, retrieving all neighbors in Θ(deg(𝑢)) time
  - Given 𝑢, 𝑣, checking if (𝑢, 𝑣) is an edge takes Θ(deg(𝑢)) time.
  - Space: Θ(𝑉 + 𝐸).
- Adjacency Matrix
  - A 𝑉 × 𝑉 matrix.
  - Given node 𝑢, retrieving all neighbors in Θ (𝑉)  time
  -  Given 𝑢, 𝑣, checking if (𝑢, 𝑣) is an edge takes 𝑂(1) time.
  -  Space: Θ(𝑉2).  

#### Trees

connected && no cycle => tree
no cycle => forest(several trees)

After we have run BFS or DFS on an *undirected* graph, the edges can be classified into 3 types:

- **Tree edges:**
  traversed by the BFS/DFS
- **Back edges:**
  connecting a node with one of its ancestors(other than its parent)
- **Cross edges:**
  connecting two nodes with no ancestor/descendent relationship.

## Algorithm

### BFS

> span a tree with NO **back** edges 

#### pseudo-code

```pseudocode
initialize an empty queue Q
Enqueue(Q,r)
while Q not empty do
	n = Dequeue(Q)
	for each v in Adj(n)
		if v.status = unknown
			v.status = processing
			v.d = n.d + 1
			v.p = n
			Enqueue(Q,v)
		n.status = over
```

Running time: Θ(𝑉+𝐸), which is Θ𝐸 if the graph is connected

#### Application

Find connected components.

### DFS

> span a tree with NO **cross** edges 

#### pseudo-code

```pseudocode
DFS(G)
for each vertex n in V do
	if n.status = unknown then
		DFS-Visit(n)

DFS-Visit(n):
n.status = processing
for each v in Adj(n) do
	if v.status = unknown the 
		v.p = n
		DFS-Vist(v)
n.status = over
```

Running time: Θ(𝑉+𝐸)

#### Application

Cycle detection

### Topological Sort

> A topological ordering of a graph is a linear ordering of the vertices of a DAG(Directed Acyclic Graph) such that if `(u,v)` is in the graph, `u` appears before `v` in the linear ordering.

#### idea

1. Output a vertex `u` with in-degree zero in current graph
2. Remove `u` and all edges `(u,v)` from current graph
3. If the graph is not empty, go to step 1

#### pseudo-code

```pseudocode
Initialize Q to be an empty queue
for each u in V do 
	If inDegree(u) = 0 then 
		% find all starting vertices
		Enqueue(Q,u)
while Q not empty
	u = Dequeue(Q);
	Output u
	for each v in Adj(u) 
		% remove u's outgoing edges
		inDegree(v) = inDegree(v) - 1
		if inDegree(v) = 0 then 
			Enqueque(Q,v)
return
```

Running time: 𝑂(𝑉+𝐸)