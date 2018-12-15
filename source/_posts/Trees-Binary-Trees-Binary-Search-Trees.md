---
title: 'Trees, Binary Trees, Binary Search Trees'
date: 2018-12-05 16:40:12
categories: I don't know about C++
tags: [data structure, Cpp]
---
# Binary Tree
Inorder traversal  
preorder traversal  
postorder traversal  

# Binary Search Tree

## BST Insertion
Always insert element at bottom as leaf.    
Remember to check if the element already exists in the tree.

### Recursion

```cpp
bool insert(const T& x, Node*& n )	// pass by reference
Node* insert(const T& x, Node* n )	// pass by value need return pointer
{
    // base case 1: reach the node beyond leaf
    if(!n) {n->x = new T(x);return true / new T(x);}
    // base case 2: the data already exists
    if(n->x == x) return false / nullptr ;
    if(x < n->x) return insert(x,n->left);
    else return insert(x,n->right);
}
```
The insert function parameter `Node*&` is a **reference**.
We always pass **reference** to it, thought the **value** of the reference may be `nullptr`. 
e.g. `insert(x,t->left)` .In this case, `t` always refers to a `Node*`.

### Iteration

```cpp
bool insertIterative(const T& x, Node* p )	// pass by value
{
    // A new pointer to refer to the leaf
    Node* pp = nullptr;
	for(;p!=nullptr;)
    {
        // already exists
        if(p->x == x) {return false;}
        pp = p; // save parent for reference
    	if(p < p->x) p = p-> left;
    	else p = p->right;
    }
    // root is nullptr
    if(!pp) root = new T(x);
    else pp = new T(x);
}
```

We only need to know the **value** of the `root` of the tree.
But we need to record the reference of the parent of the insertion node.

## BST Search

*public* interface:

```cpp
bool contain(const T& x) const
{
    
 	Node* p = root;
    Node* pp = nullptr;
    // Non-recursion method COMMON
    return search(x,p,pp);
    // Recursion method
    return searchRecur(x,p) != nullptr;
    
}
```

The implementation of `search(x,p)` and `searchIterative(x,p,pp)` is as below.

### Recursion

can **NOT** keep track of the parent node pointer.
```cpp
Node* searchRecur(const T& x, Node* n )	// pass by value
{
    // base case 1: reach the node beyond leaf
    if(!n) {return nullptr;}
    // base case 2: find the data
    if(n->x == x) return n;
    if(x < n->x) return search(x,n->left);
    else return search(x,n->right);
}
```

### Iteration

```cpp
bool search(const T& x, Node*& n, Node*& pn )// pass by reference
{	
	for(;n!=nullptr;)
    {
        if(n->x == x) {return true;}
        pn = n;
    	if(x < n->x) n = n-> left;
    	else n = n->right;
    }
    return false;
}
```

Can keep track of parent node pointer.
Note the `Node*& n` and `Node*& pn` is pas by **reference**.
They must be created before calling the function

## BST deletion

need `findMin()` function to help.

```cpp
Node* findMin(Node* p)
{
    if(!p)return nullptr;
    while(p->left)p=p->left;
    return p;
}
```

1. Delete a node with no child :

   pointer: `current` `parent`
   Simply delete it and make its parent's original pointer to it `nullptr`
2. Delete a node with one child :

   pointer: `current` `parent` `child` 
   Pass its pointer to the only child to its parent's pointer to it. Then delete it.
3. Delete a node with two child :

   pointer: `current` `min/max` `parent of min/max`
   **Override** its data to the *max* of *left* or *min* of *right*    
   **Actually delete** the min/max node of the BST, which has no child 

### Recursion

```cpp
bool remove(const T& x,Node*& n)	// pass by reference
{
    // Base case: NOT found
    if(!n) return false;
    // Recursive steps
    if(x<n->x) return remove(x,n->left);
    if(x>n->x) return remove(x,n->right);
    // node with no child
    if(!n->left && !n->right)
    {
        delete n;
        n = nullptr;
    }
    // node with two children
    else if(n->left && n-<right)
    {
        Node* rightMin = findMin(n->right);
        n->x = rightMin->x;
        // always true
        remove(n->x,rightMin);
    }
    // node with only one child
    else
    {
        Node* child = root->left?root->left:root->right;
        // save &n to curr, later delete
        Node* curr = n;
        n = n->child;
        delete curr;
    }
    return true;
}
```



### Iteration
Need the parent node pointer of *Min* node.
Hard to implement.