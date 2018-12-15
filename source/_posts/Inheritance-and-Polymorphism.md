---
title: Inheritance and Polymorphism
date: 2018-12-05 14:31:16
categories: I don't know about C++
tags: [Cpp] 
---
## Inheritance 

_Inheritance_:    describe "is-a relationship"  
_Polymorphism_:   derived class can perform like base class(object,pointer,reference)

**_Construction Order_: from inner to outer   
_Destruction Order_: from outer to inner**

### member access control

_public_:   

member functions of the class(inner)    
any member funtions of other classes(other class)   
any global functions(outside)   

_protected_:

member functions and _friends_ of the class 
member functions and _friends_ of its _derived classes_ 
**NOT** for outside functions   

_private_:

member functions and friends of the class

> Without inheritance, private and protected have exactly the same meaning.

### public protected private inheritance

inheritance\member     |public     |protected  |private 
-----------------------|-----------|-----------|--------
public inheritance     |public     |protected  |private 
protected inheritance  |protected  |protected  |private
private inheritance    |private    |private    |private

Public inheritance implements the "is-a" relationship

Private inheritance is similar to "has-a" relationship

### Friend

friend of `Base` is not friend of `Derived`

## Polymorphism

###  Dynamic Binding&Virtual Function

*Once a method is declared __virtual__ in the base class, it is automatically __virtual__ in all directly and indirectly derived classes.*

**Even if derived classed do not announce it's virtual functions.**

Virtual destructor can make deleting a base pointer to derived object operate correctly.

Do not rely on the virtual function mechanism during the execution of
a constructor.

Similarly, if a virtual function is called inside the base class destructor,
it represents base class’ virtual function: when a derived class is being
deleted, the derived-specific portion has already been deleted before
the base class destructor is called.

### Abstract Base Class(ABC):

**NO** object of ABC can be created

Its **derived classes** must implement the pure virtual functions.

ABC is just an interface.

