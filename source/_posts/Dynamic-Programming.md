---
title: Dynamic Programming
date: 2018-12-15 20:48:57
categories: Algorithm and Graph
tags: [DP, algorithm]

---

Main idea of DP:

1. Analyze the structure of an optimal solution

2. Recursively define the value of an optimal solution

3. Compute the value of an optimal solution (usually bottom-up)



[TOC]

# One-Dimension

## Stairs Climbing 1D

> You can  climb 1 or 2 stairs with one step.
> How many different ways can you climb **n** stairs?

### Recurrence

> F(n) = F(n-1) + F(n-2)

### Base case

> F(1) = 1, F(2) = 2

### pseudo-code

```pseudocode
F(n):
allocate an array A of size n
A[1] = 1
A[2] = 2
for i=3 to n
	A[i] = A[i-1] + A[i-2]
return A[n]
```

### Analyze

Running time: Θ(𝑛)
Space: Θ(𝑛) but can be improved to Θ(1) by freeing array.

## Rod Cutting Problem

> Given a rod of length **n** and prices **p~i~** for i = 1,...,n, where **p~i~** is the price of a rod of length **i** . Find a way to cut the rod to maximize total revenue.

| length i   | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| ---------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| price p~i~ | 1    | 5    | 8    | 9    | 10   | 17   | 17   | 20   | 24   | 30   |

### Recurrence

total **optimal** revenue is price of cut rod and **optimal** revenue of remaining rood.

> r~n~ = max{p~n~, p~1~ + r~n-1~, p~2~ + r~n-2~, ..., p~n-1~+r~1~}

### Base case

Define **r~n~** be the maximum revenue obtainable from cutting a rod of length **n**.

> r~1~ = p~1~

### pseudo-code

```pseudocode
cutRod(n):
let r[0...n] and s[0...n] be new arrays
r[0] = 0 
for j=1 to n		% every optimal length
	q = -inf
	for i =1 to j 	% every cut length
		if q < p[i] + r[j-i] then
			q = p[i]+r[j-i]
			% keep track the optimal cut length
			s[j] = i
	r[j] = q
j = n 
while j>0 do 
	print s[j]
	j = j-s[j]
```

| i    | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| p[i] | 0    | 1    | 5    | 8    | 9    | 10   | 17   | 17   | 20   | 24   | 30   |
| r[i] | 0    | 1    | 5    | 8    | 9    | 13   | 17   | 18   | 22   | 25   | 30   |
| s[i] | 0    | 1    | 2    | 3    | 2    | 2    | 6    | 1    | 2    | 3    | 10   |

### Analysize

Running time: Θ(𝑛^2^)
Space: Θ(𝑛) 

## Weighted Interval Scheduling 1D

> Jobs **j** starts at **s~j~**, finish at **f~j~** and has weight(value) **v~j~**.
> Two jobs compatible if they don't overlap.
> Goal: find maximum-weight subset if mutually compatible jobs.

### Recurrence

Firstly, label all jobs by **finishing time**.

**Maximum** subset is either
DO take this job and **maximum** subset of *compatible* jobs set
NOT take this job and **maximum** subset of *remaining* jobs

> V[j] = max{v~j~ + V[c(j)], V[j-1]}

function `c(j)` return the largest index i<j such that job i is compatible job j.

### Base case

The goal is to find a subset of a set.

> We start from a **empty set**.

### pseudo-code

```pseudocode
schedule():
sort all jobs by finish time
V[0] = 0
for i = 1 to n
	% DO take job j
	if V[i] + V[c(i)] > V[j-1] then
		V[i] = value[i] + V[c(i)]
		keep[i] = 1
	% NOT take job j
	else 
		V[i] = V[i-1]
		keep[i] = 0 
i = n
while i > 0 do
	if keep[i] =1 then print i, i = c(i)
	else i = i-1
```

### Analyze

Running time: Θ(𝑛log𝑛)
Space: Θ(𝑛) 

# Two-Dimension

Sometimes sub-problem also need to use 1D-DP to solve.
Sometimes there are two variables that requires a 2D array.

## The 0/1 Knapsack Problem

> A set of **n** items, where item **i** has weight **w~i~** and value **v~i~** ,and a knapsack with capacity **W**.
> Find **x~1~,... ,x~n~** (either 0 or 1) such that 
> sum(**x~i~w~i~**) <= **W ** and **V**=sum(**x~i~v~i~**) is maximum

### Recurrence

Start to put items into the knapsack.**--1D**

**Maximum** V of capacity W is the max among <u>whether</u> take each item.
v~i~ + **maximum** XV of capacity j-w~i~ 
*We found it necessary to consider capacity of knapsack.***--2D**


> V[j] = max{v(i) + V[j-w(i)]}	i from 1 to n

***WRONG: This may pick the same item more than once!
Thus, both in v~i~ and V[j-w(i)]!***

New def: let V[i,j] be the largest obtained value with capacity j, choosing *ONLY from the first i items.
Below formula truly reflect <u>whether</u>. Left is NOT, right is YES.

> V[i,j] = max{V[i-1,j], v~i~ + V[i-1,j-w(i)]} 	i from i to n

### Base case

> We start from **take nothing**: i= 0, V[0,j] = 0
> and **empty knapsack** j =0, V[i,0] = 0

### pseudo-code

```pseudocode
let V[0...n,0...W] and keep[0...n,0...W] be new arrays of all 0	
for i = 1 to n do		% put things
	for j = 1 to W do 	% each capacity
		if w[i]<=j and v[i]+V[i-1,j-w[i]] > V[i-1,j] then
			V[i,j] = v[i]+V[i-1,j]
			keep[i,j] = 1
		else 
			V[i,j] = V[i-1,j]
			keelp[i,j] = 0
K = W
for i = n downto 1 do 
	if keep[i,K] = 1 then 
		print i
		K = K-w[i]
```

input:

i|1|2|3|4
-|-|-|-|-
v~i~|10|40|30|50
w~i~|5|4|6|3

running:

V[i,j]|0|1|2|3|4|5|6|7|8|9|10
------|-|-|-|-|-|-|-|-|-|-|------
i=0|0|0|0|0|0|0|0|0|0|0|0
1|0|0|0|0|0|10|10|10|10|10|10
2|0|0|0|0|40|40|40|40|40|40|40
3|0|0|0|0|40|40|40|40|40|50|70
4|0|0|0|50|50|50|50|90|90|90|90



### Analyze

Running time: Θ(𝑛𝑊)
Space: Θ(𝑛𝑊) 
without `keep` array can be improved to Θ(𝑛+𝑊).

## Longest Common Subsequence LCS

>Given two sequences **X** = (x~1~,x~2~,...,x~m~) and **Y** = (y~1~,y~2~,...,y~n~), we say that **Z** is a common subsequence of **X** and **Y** if **Z** has a strictly increasing sequence of indices i and j of both **X** and **Y** such that we have x~ip~= y~jp~ = z~p~  for all p = 1, 2, … , k. The goal is to find the longest common subsequence of **X** and **Y**.
>
>![1544897951063](/assets/Dynamic-Programming/1544897951063.png)
>

### Recurrence

Let c[i,j] be the length of the **longest** common subsequence of X[1...i] and Y[1...j].
So we need **2D** that i for X and j for Y.

Firstly, go through i, and then go through j
If X[i] = Y[j], we match.
c[i,j] = 1 + the **longest** common subsequence of X[1...i-1] and Y[1...j-1].
else , it's max of 
the **longest** common subsequence of X[1...i-1] and Y[1...j] and 
the **longest** common subsequence of X[1...i] and Y[1...j-1]

> If X[i] = Y[j] , c[i,j] = max{ c[i,i-1], c[i-1,j]  }
>
> else c[i,j]= c[i-1,j-1] +1

### Base case

> c[0,j] =0 
> c[i,0] = 0

### pseudo-code

```pseudocode
LSC(X,Y):
let c[0...m,0...n] and b[0...m,0...n] be new arrys of all 0
for i=1 to m
	for j=1 to n
		if X(i) = Y(i) then
			c[i,j] = c[i-1,j-1]+1
			b[i,j] = "↖"
		else if c[i-1,j] >= c[i,j-1] then
			c[i,j] = c[i-1,j]
			b[i,j] = "↑"
		else
			c[i,j] = c[i,j-1]
			b[i,j] = "←"
Print-LCS(b,m,n)

Print-LCS(b,i,j):
	if i=0 or j=0 then return 
	if b[i,j]="↖" then
		Print-LCS (b,i−1,j−1)
		print X(i)
	else if 𝑏[𝑖,𝑗]="↑"
		Print-LCS( b,i−1,j)
	else Print-LCS( b,i,j−1)
```

![1544897610939](/assets/Dynamic-Programming/1544897610939.png)

### Analyze

Running time: Θ(𝑚𝑛)
Space: Θ(𝑚𝑛) 
without `b` array can be improved to Θ(𝑚+𝑛).



## Longest Common Substring

>Given two strings **X** = x~1~x~2~...x~m~ and **Y** = y~1~y~2~...y~m~, we wish to find their longest common substring **Z**, that is, the largest **k** for which there are indices 𝑖  and 𝑗 with 
>x~i~x~i+1~...x~i+k-1~ = y~j~y~j+1~...y~j-k-1~
>
>![1544898031881](/assets/Dynamic-Programming/1544898031881.png)

### Recurrence

Let d[i,j] keep track of k, the **longest** string length of X[1...i] Y[1...j].
Firstly go through X and then Y so we need **2D**

If X(i) = Y(j), d[i,j] = 1 +  the **longest** string length of X[1...i-1] Y[1...j-1].
else it's 0!

> If X(i) = Y(j), d[i,j] = 1 + d[i-1.j-1]
> else d[i,j] = 0

### Base case

> d[0,j] = 0, d[i,0]= 0

### pseudo-code

```pseudocode
let d[0...m,0...n] be a new array of all 0
length = 0, endIndex = 0
for i = 1 to m
	for j = 1 to n
		if X(i) = Y(i) then 
			d[i,j] = d[i-1,j-1] + 1
			if d[i,j] > length then 
				length = d[i,j]
				endIndex = i
for i = endIndex - length + to endIndex
	print X(i)
```

### Analyze

Running time: Θ(𝑚𝑛)
Space: Θ(𝑚𝑛) but can be improved to Θ(𝑚+𝑛).

# Over Intervals

- Goal is to find optimal (min or max ) solution on problem with
  - Problem of size n
  - *Ordered* input of items 1,2,...n
- Define substructures as 
  - *Ordered* input of items i..j
  - Problem of size j-i+1
- Recurrence gives optimal solution of subproblem
   as function of optimal solution of smaller subproblems
- Algorithm fills in DP table from smallest to largest problem size
- Often, final subproblem filled is solution for original problem
  Sometimes, solution of original problem is  min/max over table values

## Longest Palindromic Substring 

A palindrome is a string that reads the same backward or forward.

> Given a string **X** = x~1~x~2~...x~n~, find the longest palindromic substring.
>
> Ex:
> X =ACCABA
> Palindromic substrings: CC, ACCA ABA
> Longest palindromic substrings: ACCA

### Recurrence

Let p[i,j] be *true* if and only if X[i...j] is a palindrome.
Obviously, we need **2D**, though i <= j. It's like a half plane.

If X[i] = X[j], p[i,j] is **true** iff p[i+1,j-1] is **true**.

> If X(i) = X(j), p[i,j] = p[i+1,j-1]

Order: from **(i,j)** to **(i+1,j-1)** it's a **diagonal** path.

### Base

> If i = j, p[i,j] = true

### pseudo-code

```pseudocode
let p[0...n,0...n] be a new array of all false
max = 1 
for i = 1 to n-1 do 
	p[i,i] = true
	if X(i) = X(i+1) then 
		p[i,i+1] = true
		max = 2
% updating along diagonal
% started from the third diagonal
for k = 3 to n do
	for i = 1 to n-k+1 do
		j = i+k-1
		if p[i+1,j-1] = true and X(i) = X(j) then
			p[i,j] = true
			max = k
return max
```

### Analyze

Running time: 𝑂(𝑛^2^)
Space: 𝑂(𝑛^2^) but can be improved to 𝑂(𝑛)

## Optimal BST

> Given **n** keys a~1~ < a~2~ < ... < a~n~, with weights f(a~1~), … , f(a~n~), find a binary search tree **T** on these **n** keys such that
> **B(T)** = sum{ f(a~i~)\*(d(a~i~)+1) } i from 1 to n
> is minimized, where d(a~i~) is the depth of a~i~.

### Recurrence

Let T~i,j~ be some tree on the subset of nodes a~i~ < a~i+1~ < ... < a~j~ 
Define w[i,j] = f(a~i~) + ... + f(a~j~)
The cost is defined as B(T~i,j~) = sum{ f(a~i~)\*(d(a~i~)+1) } from i to j
Define e[i,j] = optimal value of B(T~i,j~)

The **optimal** cost of T~i,j~ is 
The **optimal** cost of left subtree + The **optimal** cost of right subtree + root's weight

> e[i,j] = e[i,k-1 + e[k+1,j] +w[i,j]

To find **k**, try every value between **i** and **j** to figure out the min.

> e[i,j] = min{ e[i,k-1 + e[k+1,j] +w[i,j] } for i<=k<=j

Order: (i,j) (i,k-1) (k+1,j)![my](/assets/Dynamic-Programming/BST-k.png)

### Base

> e[i,j]= 0 for i>j
> e[i,i] = f(a~i~) for all i

### pseudo-code

```pseudocode
Optimal-BST(a,n):
let e[1...n,1...n],w[1...n,1...n],root[1...n,1...n] be new arrays of all 0
for i = 1 to n 
	w[i,i] = f(a(i))
	for j = i + 1 to n
		% complete the w[] table
		w[i,j] = w[i,j-1] + f(a(i))
for l = 1 to n
	for i = 1 to n-l+1
		j = i+l-1
		e[i,j] = inf
		% find k minimizes e[]
		for k = i to j
			t = e[i,k-1]+e[k+1,j]+w[i,j]
			if t < e[i,j] then
				e[i,j] = t
				root[i,j] = k
return Construct-BST(root,1,n)

Construct-BST(root,i,j):
if i > j then return nil
create a node z
z.key = a[root[i,j]]
z.left = Construct-BST(root,i,root[i,j]-1)
z.right = COnstruct-BST(root,root[i,j]+1,j)
return z
```

### Analyze

Running time: Optimal 𝑂(𝑛^3^) Construct 𝑂(𝑛^2^)
Space 𝑂(𝑛^2^)

![13_BST_页面_02](/assets/Dynamic-Programming/13_BST_页面_02.png)
![13_BST_页面_03](/assets/Dynamic-Programming/13_BST_页面_03-1544905264572.png)![13_BST_页面_04](/assets/Dynamic-Programming/13_BST_页面_04.png)
![13_BST_页面_05](/assets/Dynamic-Programming/13_BST_页面_05.png)
![13_BST_页面_07](/assets/Dynamic-Programming/13_BST_页面_07.png)
![13_BST_页面_09](/assets/Dynamic-Programming/13_BST_页面_09.png)
![13_BST_页面_12](/assets/Dynamic-Programming/13_BST_页面_12.png)
![13_BST_页面_13](/assets/Dynamic-Programming/13_BST_页面_13.png)