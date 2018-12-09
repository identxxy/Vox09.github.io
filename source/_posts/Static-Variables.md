---
title: Static Variables
date: 2018-12-05 15:56:53
categories: I don't know about C++
tags: [static variable, Cpp]
---
## Static variables

### Global Scope

- created only once in a program.
- reside on the static data region of the loaded program.
- have a lifetime across the entire run of a program.
- still may have limited scope: file, function, class.

### Function Scope

- initialized only once regardless how many times the function is called.
- retain their values across the function calls.
- can be accessed **only inside the function.**

### Class Scope

- variables in different objects are actually one variable.

- initialize variable must be done in **global scope**
  **cannot initialize it inside the class.**

  > **NOT in any function! Not in main(), either!**
  > **NO** keyword `static`
  > e.g.
  >
  > ```cpp
  > class A {static int a;};
  > int A::a = 10;
  > int main(){}
  > ```

- **variable exists even if no class object is created.**

## Static Functions

### Global Functions

- almost same as [static variables](#global-scope).

###  Member Functions

- do not have the implicit this pointer like regular non-static member functions.

- may be used even when there are **no** objects of the class!

- can **only** make use of static data members of the class.

- cannot be `const` nor `virtual` functions.

- can be defined outside the class declaration

  > **NO** keyword `static`
  >
  > e.g.
  >
  > ```cpp
  > class A {static void f();};
  > void A::f(){}
  > int main(){}
  > ```
  >
  >