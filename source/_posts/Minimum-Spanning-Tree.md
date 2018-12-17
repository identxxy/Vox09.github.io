---
title: Minimum Spanning Tree
date: 2018-12-15 21:00:00
categories: Algorithm and Graph
tags: [searching, graph, algorithm]

---

![1544942034490](/assets/Minimum-Spanning-Tree/1544942034490.png)

## Uniqueness of MST

### Cut Lemma

Let `S` be any subset of nodes
let `e` be the *min* cost edge with exactly one endpoint in `S`. 
Then every MST must contain `e`.

### Proof

Let `T*` be some MST
Consider any edge `e` in `T*`
Removing `e` from `T*` breaks `T*` into two parts `S`and `V-S`
`e` must be the min cost edge crossing the cut `(S,S-V)`
Applying the cut lemma on `S`, every MST must contain `e`
Apply the above arguments to every edge in `T*`, we have
_Every edge `e` in `T*` must be contained in every MST_



## Prim's Algorithm

![1544950977399](/assets/Minimum-Spanning-Tree/1544950977399.png)

![1544951001949](/assets/Minimum-Spanning-Tree/1544951001949.png)

### Idea

1. Initialize explored set `S` = { *any one node* }
2. Add min cost edge `e` = `(u,n)` with `u` in `S` and `n` in `V-S` to `T`
3. Add `n` to `S`
4. Repeat until `S = V`

### Feature

Maintain set of explored nodes `S`

A Min priority queue `Q` to keep unknown nodes.
key is their **cheapest** edge to node in`S`

### pseudocode

```pseudocode
Prim(G,r):
for each n in V do 
	n.key = inf, n.p = nil, n.status = unknown
r.key = 0
create a min priority queue Q on V
while Q not empty
	u = Extract-Min(Q) 	% need O(logV) time
	u.status = over
	for each n in Adj(u) do
		if n.status = unknown and w(u,n)<n.key then
			n.p = u
			n.key = w(u,n)
			Decrease-Key(Q,n,w(u,n))
```

Running time: 𝑂(𝐸log𝑉)

## Kruskal's Algorithm

![1544951029652](/assets/Minimum-Spanning-Tree/1544951029652.png)
![1544951067674](/assets/Minimum-Spanning-Tree/1544951067674.png)

### Idea

1. Start with an empty tree `T`
2. Consider edges in ascending order of weight.
3. Case 1: If adding `e` to `T` create a cycle, discard `e`
   Case 2: Otherwise, insert `e = (u,v)` into `T` according to [cut Lemma](#cut-lemma)

### Union-Find Data Structure

#### Key Question

How to check whether adding `e` to `T` will create a cycle?

- DFS take 𝑂(𝐸⋅𝑉) time in total.
  Can we do better in 𝑂(log𝑉) time?
- After an edge `e` is added, two sets **union** together
- Need such a "union-find"data structure:
  - Find-Set(u): For a given node `u`, find which **set** this node belongs to.
  - Union(u,v): For two given nodes `u` and `v`, merge the two **sets** containing `u` and `v` together.

#### Set as A Tree

- The trees in the union-find data structure are *NOT* the same as the partial MST trees!
  ![1544952591584](/assets/Minimum-Spanning-Tree/1544952591584.png)

- The root of the tree is the representative node of all nodes in that tree 
  (i.e., use the root’s ID as the unique ID of the set).

- Every node (except the root), has a pointer pointing to its parent.

  - The root has a parent pointer to itself.

  - No child pointers (unlike BST), so a node can have many children.

    ```pseudocode
    Make-Set(x):
    x.parent = x, x.hight = 0
    ```

    ```pseudocode
    Find-Set(x):
    x.height = 0
    while x!= x.parent do
    	x.height = x.height + 1
    	x= x.parent
    return x
    ```

    ```pseudocode
    Union(x,y):
    a = Find-Set(x)
    b = Find-Set(y)
    if a.height <= b.height then
    	if a.height = b.height then
    		b.height = b.height + 1
    	a.parent = b
    else b.parent = a
    ```

##### Path Compression

while `Find-Set(x)` we can update its `parent` pointer to compress the path.

![1544954929318](/assets/Minimum-Spanning-Tree/1544954929318.png)



### pseudocode

```pseudocode
MST-Kruskal(G):
for each node n in V
	Make-Set(n)
sort the edges of G into increasing order by weight % O(ElogE)
for each edge (u,n) in E taken in the above order
	if Find-Set(u) != Find-Set(v) then				% O(E)
		output edge (u,n)
		Union(u,n)
```

Running time: 𝑂(𝐸log𝐸+𝐸log𝑉)=𝑂(𝐸log𝑉)
Note: If edges are already sorted(𝑂(𝐸log𝐸)) and we use [path compression](#path-compression), then the running time is close to 𝑂(𝐸)