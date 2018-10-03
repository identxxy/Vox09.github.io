---
title: Cpp Notes
categories: tech
date: 2018-05-27 16:37:08
tags: Cpp
---

### About private member
1 在C+的类的成员函数中，允许直接访问该类的对象的私有成员变量
2 在类的成员函数中可以访问同类型实例的私有变量量
3 拷贝构造函数里，可以直接访问另外一个同类对象（引用）的私有成员
4 类的成员函数可以直接访问作为其参数的同类型对象的私有成员

Below codes work!!
> private:
>     int data;
> public:
>     void lookOtherConst(const A& a){cout<<"other's private data is "<<a.data<<endl;}
>     void lookOtherReference(A& a){cout<<"other's private data is "<<a.data<<endl;}
>     void lookOther(A a){cout<<"other's private data is "<<a.data<<endl;}
> 
### About f\*cking const
[here](http://www.cnblogs.com/jiabei521/p/3335676.html)

### 前缀 与 后缀 运算符
[前缀](https://msdn.microsoft.com/zh-cn/library/dy3d35h8.aspx)：**在表达式计算中使用值之前递增或递减**，表达式的值与操作数的值**不同**
[后缀](https://msdn.microsoft.com/zh-cn/library/e1e3921c.aspx)：**在表达式使用值之后递增或递减**， 表达式的值与操作数**相同**
e.g.
> int a=5,b =5;
> cout<<"a<b++ = "<<bool(a<b++)<<endl;
> cout<<"a<++b = "<<bool(a<++b)<<endl;
> 
> output:
> a<b++ = 0 
> a<++b = 1
