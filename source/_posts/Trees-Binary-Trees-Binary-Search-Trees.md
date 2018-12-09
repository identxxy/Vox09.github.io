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
always insert element at bottom as leaf.    
remember to check if the element already exists in the tree.
### Recursion
*First check how many children one node has!! Don't access **nullptr**!*

**node type**               |operation
----------------------------|---------------------------
**node with two children:** |recurive left or right
**node with left child:**   |recursive right or insert left 
**node with right child:**  |recursive left or insert right 
**node with no child:**     |insert left or right

### Non-Recursion
> while !nullptr    
> if less p=p->left  
> else if greater p=p->right
> else return // alreay exist!  

Then p = nullptr is the place to insert.    
Modfy its parent pointer pp to complete the operation.   

## BST Search
### Recursion
> if node == nullptr return **false**   
> if node->data == data return **true**  
> if less recursive left  
> if greater recursive right  

### Non-Recursion
> while !nullptr    
> if less p=p->left  
> else if greater p=p->right  
> else return **true**  
> return **false**  

## BST deletion
### Delete a node with no child
> pointer: current, parent    

Simply delete it and make its parent's original pointer to it **nullptr**
### Delete a node with one child
> pointer: current, parent, child    

Pass its poniter to the only child to its parent's pointer to it.  
Then delete it.
### Delete a node with two children
> pointer: current, min/max, **parent of min/max**    

**Override** its data to the *max* of *left* or *min* of *right*    
**Actually delete** the min/max node of the BST, which has no child 
### Implementation
1. search for the node  
2. check how many child     
3. if no :delete it; parent's pointer to **nullptr**    
    if one: by pass pointer and delete  
    if two: get min/max; **override** data; delete min/max; parent's pointer to **nullptr**  


