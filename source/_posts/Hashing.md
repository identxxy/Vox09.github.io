---
title: Hashing
date: 2018-12-07 02:05:54
categories: School Notes
tags: [data structure, hashing]
---

# Hash Table
A hash table is an array of some **fixed size**,
containing all the data items.  
Each item has a **key** search is performed based on the keys.  
Each key is **mapped** into some position in the array in the range 0 to m − 1, where m is the
array size.     
The mapping is called **hash function**.

Hashing table is a data structure that supports: 
1. searching
2. insertion
3. deletion  

The implementation of hash tables is called **hashing**.  
Hashing is a technique which allows the executions of above operations in **constant time** on average.  
Unlike other data structures such as linked list or binary trees, data items are generally **not** ordered in hash tables.  
As a consequence, hash tables don't support the following operations:   

1. find_min and find_max
2. finding successor and predecessor
3. reporting data with a given range
4. listing out the data in order

# Hashing Function 
## Design
A simple and reasonable strategy: `h(k) = k mod m.`  

Its a good practice to set the table size m to a prime number.  
> h(key) =(key[0] + 37\*key[1] + 37^2\*key[2] + ... ) mod **m**

## Handling collision

**Make hash table an array of linked lists**  

### Seperate Chaining

#### Insertion

To insert a key **k**:  
> Compute h(k)  
> If T(h(k)) contains `nullptr` , initial this table entry to point to linked list node of `kk` .  
> If T(h(k)) contains `non-nullptr`, add `k` to the beginning of the list.

#### Deletion

To delete a key **k**:  
> Compute h(k) to determine which list to traverse.  
> Search for the key `k`, in the list that T(h(k)) points to.      
> Delete the item with key `k` if it is found.  

### Open addressing

Instead of putting keys of the same hash table into a chain, open addressing will **relocate** the key k to be inserted if it collides with an existing key.
#### Linear probing
> f(i) = i  
> hi(k) = (hash(k) + i) mod m  

Disadvantage: fall into cluster   
#### quadratic probing

> f(i) = i^2^  
> hi(k) = (hash(k) + i^2^) mod m  

Disadvantage: second cluster... same key same probe steps.    
#### double hashing

> f (i) = i ×  hash2(k)    
> h~i~(k) = (hash(k) + i × hash~2~(k)) mod m

hash~2~(k) must be **relatively prime** to the table size m.  
Otherwise, we will only be able to examine a fraction of the table entries.