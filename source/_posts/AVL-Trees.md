---
title: AVL Trees
date: 2018-12-06 17:06:19
categories: I don't know about C++
tags: [data structure, Cpp]
---
*(All codes below are written by Dr. Desmond)*
# AVL(Adelson-Velsky and Landis) Trees
Every sub-tree of an AVL tree is itself an AVL tree.(An empty tree is an AVL tree too.)  
```cpp
struct AVLNode{  
    T data;  
    int height;     // Important!
    AVLNode* left;  
    AVLNode* right;  
}  
```

## AVL Tree Searching
same as BST searching
## AVL Tree Insertion
Insertion may violate the AVL tree property in 4 cases:
1. Insertion into the **right** sub-tree of the **right** child     
Left(anti-clockwise) rotation [single rotation]  
2. Insertion into the **left** sub-tree of the **left** child  
Right(clockwise) rotation [single rotation]  
3. Insertion into the **right** sub-tree of the **left** child     
Left-right rotation [double rotation]    
4. Insertion into the **left** sub-tree of the **right** child     
Right-left rotation [double rotation]    

**!!! Distinguish L/R of subtree or L/R of child !!!**    

**First insert the node to right place, then balance it by rotation.**  
```cpp
AVLNode<T>* AVL<T>::insert(AVLNode<T>* p, const T& d)
{
    if( !p ) return new AVLNode<T>(d);  // Empty AVL tree
    if( d < p->data )
        p->left = insert(p->left, d);   // Recursion on the left sub-tree
    else
        p->right = insert(p->right, d); // Recursion on the right sub-tree
    return balance(p);
}
```
### Rotation
Single rotation operate on the **node**, replace original node by its child    
Double rotation first operate on the **child**, then on the **node**, replace original node by its grandchild. 

```cpp
AVLNode<T>* AVL<T>::rotateRight(AVLNode<T>* p)
{
    AVLNode<T>* pl = p->left;
    p->left = pl->right;
    pl->right = p;
    updateHeight(p);
    updateHeight(pl);
    return pl;
}

AVLNode<T>* AVL<T>::rotateRightLeft(AVLNode<T>* p)
{
    rotateRight(p->right);
    return rotateLeft(p);
}

AVLNode<T>* AVL<T>::balance(AVLNode<T>* p)
{
    h->height = max(h->right->height,h->left->height);
    // heightDiff() return (right subtree height) - (left subtree height)
    if( heightDiff(p) == 2 ){
        if( heightDiff(p->right) < 0 )  // if need double rotation
            p->right = rotateRight(p->right);
        return rotateLeft(p);           // single rotation
    }
    if( heightDiff(p) == -2 ){
        if( heightDiff(p->left) > 0 )   // if need double rotation
            p->left = rotateLeft(p->left);
        return rotateRight(p);          // single rotation
    }
    return p;
}
```
## AVL Tree Deletion
The idea is same as BST.  
When delete node with two children, the following code **delete the node and 
rise the min node up**.   
Then balance the whole tree.
```cpp
AVLNode<T>* AVL<T>::findMin(AVLNode<T>* p) const
{
    return p->left ? findMin(p->left) : p;
}

// NOT delete the Min-node!!
AVLNode<T>* AVL<T>::removeMin(AVLNode<T>* p)
{
    if(p->left == 0)                    // If it's the min node, not return itself
        return p->right;                // Return the second min means removeMin!
    p->left = removeMin(p->left);       // Recursion on the left sub-tree
    return balance(p);
}

AVLNode<T>* AVL<T>::remove(AVLNode<T>* p, const T& d) 
{
    if( !p ) return 0;                  // Item is not found; do nothing
    if( d < p->data )
        p->left = remove(p->left,d);    // Recursion on the left sub-tree
    else if( d > p->data )
        p->right = remove(p->right,d);  // Recursion on the right sub-tree
    else {                              // Item is found
        AVLNode<T>* pl = p->left;
        AVLNode<T>* pr = p->right;
        delete p;                       // Remove the node with value d
        if( !pr ) return pl;            // Return left sub-tree if no right sub-tree
        AVLNode<T>* min = findMin(pr);  // Find min. node of the right sub-tree
        min->right = removeMin(pr);     // Did NOT delete the min node on right sub-tree 
        min->left = pl;
        return balance(min);            // Balance this node
    }
    return balance(p);                  // Balance this node
}
```
##### Explanation
`p->left = remove(p->left,d);`  
Connect between parent and child. So no need to store parent pointer.   
`delete p;`     
Truly delete node with required data. But store its left and right nodes.   
Later return min of right sub-tree to its parent node. No memory leak.  
`min->right = removeMin(pr);`   
Return from the second min node and balanced from bottom up to the deleted node's right child.    
Did not truly delete the min node. **It should already be stored by `findMin()` function.**

Also refer [here](https://blog.csdn.net/u010442302/article/details/52713585)


