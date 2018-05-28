---
title: Cpp Notes
categories: tech
date: 2018-05-27 16:37:08
tags: Cpp
---
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
