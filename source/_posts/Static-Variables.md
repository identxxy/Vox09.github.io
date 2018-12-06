---
title: Static Variables
date: 2018-12-05 15:56:53
tags:study,Cpp
---
### Static variables are global variables 
> created only once in a program.
>
> reside on the static data region of the loaded program.
>
> have a lifetime across the entire run of a program.
>
> still may have limited scope: file, function, class.
### Static variables in a function
> initialized only once regardless how many times the function is called.
>
> retain their values across the function calls.
>
> can be accessed only inside the function.
### Static variables in a class
> variables in different objects are actually one varibale.
> 
> initailize variable must be done in global scope(**NOT in any function! Not in main(), either**)
>
> **variable exists even if no class object is created.**
### Static Member functions
> do not have the implicit this pointer like regular non-static member functions.
>  
> may be used even when there are **no** objects of the class!
>
> can **only** make use of static data members of the class.
>
> cannot be const nor virtual functions.
