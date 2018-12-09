---
title: AI Chap.3 Searching
date: 2018-06-28 18:15:57
categories: AI study
tags: [searching, AI, algorithm]
---

## Describe problem formally

**state**:  initial state:  
**actions**: each state corresponds to a set of available actions
**transition model**: return the result state of an action from the previous state  
**goal test**:  
**path cost**:  *the essence of a solution is a sequence of actions that lead from the initial state to the goal state.*  

## Problem formulation

**incremental formulation**: e.g. 8-queens puzzle. Initial state: no queen; Action: add a queen.
**complete-state formulation**: e.g. 8-queens puzzle. Initial state: 8 queens; Action: move a queen.  

## Search

search algorithms all use a **search** tree data structure with nodes.  
### node

n.state: correspond state in state space; 
n.parent: the node in the search tree that generated this node;  
n.action: the action that was applied to the *parent* to generate this node;  
n.path-cost: succeeded from parent;    

### other concepts

**expanding**: apply each legal action to the current state, thereby **generating** a new set of states.
**parent node** ; **child node** ; **leaf node**: a node with no child node   
**frontier(open list)**: the set of all leaf nodes avaiable for expansion  
**explored set(close list)**: the set of expanded nodes  
the frontier **separates** the state space into explored region and unexplored region  
**repeated state** ; **loopy path**: a special case of the more general concept of **redundant path**  

## Uninformed(blind) search

### tree search

not remember history causes loopy paths which is a special case of redundant path  

### graph search

add explored set(close list) to remember  

### breadth-first search  
FIFO queue; expand shallowest node;    
time complexity: O(b^d) (test while generating) O(b^(d+1)) (test while selected)    
space complexity: all nodes  

### uniform-cost search  
priority queue; expand cheapest node;  
*test while selected* because while generating it may not be the optimal one.    
time complexity: e be the minimum step-cost,C\* be the optimal cost. O(b^floor(1+(C\*/c)))    
space complexity: near nodes  

### depth-first search  
LIFO stack; first to deepest node that has no successors;  
common to implement with a recursive function, recursive depth-first search(RDFS)
time complexity: O(b^m^)  
space complexity: m is the maximum depth. O(bm)   
or O(m)( backtracking search in which expanded node remembers which successor to generate next)  

### depth-limited search

infinite state space cause depth-first search fall. It can be alleviated by a depth limit l.  

### iterative deepening depth-first search  
often used in combination with depth-first search to find the best depth limit.  
gradually increase the depth limit till the goal is found.  
upper levels generate multiple times: 

> N(IDS) = db+(d-1)b^2^+...+(1)b^d^  

### bidirectional search

> b^(d/2)^ + b^(d/2)^ < b^d^

but action step must be reversible  

### Summary

![uninformed-search](/pic/uninformed_search.png)  

## Informed(Heuristic) search

### greedy best-first search   
evaluates nodes by using just the heuristic function
> f(n) = h(n)  

 time complexity: depends on heuristic function, worst O(b^m^), m is the maximum depth.
 space complexity: depends on heuristic function, worst O(b^m^), m is the maximum depth.  

### A\* search  
>  f(n) = g(n) + h(n)

*admissible* heuristic: never overestimates the cost to reach the goal  
*consistent* heuristic: the estimated cost of **n** is no greater than **n'**  
tree-search optimal if `h(n)` is admissible; 
graph-search optimal if `h(n)` is consistent  
 absolute error: 

> E=h\*-h; 

relative error: 
> e=(h\*-h)/h  

time complexity: O(b^E^) or O(b^ed^)(constant step costs)
space complexity: all nodes that f(n) < C\*

### Memory-bounded heuristic search

#### iterative-deepening A\*(IDA\*)

> *cutoff* = f(n) - g(n) - h(n)   

each iteration cutoff value is the smallest (f-g-h) of any node exceeded the *cutoff* on the previous iteration
too little memory: between iteration, only retains *cutoff* = f-g-h

#### recursive best-first search(RBFS)

use *f_limit* variable to keep track of f-value   
use *alternative* variable to record the second-lowest f-value among successors (backed-up value)  
too little memory: each time change mind to *alternative* path, forget what it have done and regenerate nodes

#### MA\* and SMA\*

MA\*(memory-bounded A\*) and SMA\*(simplified MA\*)

SMA\* expands the best leaf until memory is full, drops the worst leaf node(highest h),   
like RBFS, SMA\* back up the value of the forgotten node to its parent

### learning to search better

**metalevel state space**: the internal(computational) state of a program that is searching in an   
**object-level state space**: the real world problem

##  Heuristic functions

#### From relaxed problems

ignore some restriction rules and estimate the cost  
relaxed problem must be able to solved **without search**  
we can use more than one heuristic function like below:  

>h(n) = max{h1(n), h2(n), ..., hm(n)}
#### From subproblems: Pattern databases

subproblems: just solve part of the problem like going just half way  
**pattern database**: store these exact solution costs for every possible subproblem instance  
**disjoint pattern database**: only consider partial cost of the subproblem that matters  

#### learning heuristics from experience  
Each solution is an example for study.  
From these examples, a learning algorithm can be used to construct a function h(n)  
to predict solution costs for other states that arise during search.  
**Inductive learning** works best if supplied with **features** of a state relevant to predicting state's value
the feature is  
> x1(n), x2(n), ..., xm(n)  

after solved the problem, we found that   
> h1(n), h2(n), ..., hm(n)  

then we approach is to use a linear combination  
> h(n) = c1\*x1(n) + c2\*x2(n) + ... + cm\*xm(n) 

## Summary

![summary-1](/pic/AI-Ch3-summary-1.png)
![summary-2](/pic/AI-Ch3-summary-2.png)

