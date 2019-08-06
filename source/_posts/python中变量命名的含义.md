[TOC]

# python中变量命名的含义

在python代码中，变量命名有很多种。比如`var`、`_var`、`__var`、`__var__`等，一直很对这些似懂非懂。本文就对Python中变量/函数命名规范总结。

因为函数名和变量都有这些不同命名的格式，所以我们以变量名为例进行梳理，函数名其实也是一样的。

先来看结论。

## 普通变量（var）

这类变量可以认为是公有变量，也就是说，在类中声明了这类变量，**该类的函数**、**该类的对象**、**该类的子类的对象**都可以对其进行访问。

## 单前导下划线（_var）

这类变量是类的内部变量，**该类的函数、该类的对象、该类的子类的对象**都可以对其进行访问。但是，使用`from module import *`时，不会被导入。

## 双前导下划线（__var）

这类变量是类的私有成员，**只允许该类的函数访问**，该类的对象和子类都无法访问。

## 前后双下划线（__var\_\_）

这种写法是一种约定，python内部的名字，用来区别其它用户自定义的命名，以防止冲突。表示这是一个特殊的成员。比如定义类的时候，若是添加`__init__()`方法，那么在创建类的实例的时候，实例会自动调用这个方法。一般不将自定义的方法这样命名。

## 代码实战

首先我们定义两个类，B是A的子类。

```python
class A:
    def __init__(self):
        self.a = 1
        self._a = 2
        self.__a = 3
    
    def func(self):
        print(self.a)
        print(self._a)
        print(self.__a)
    
    def _func(self):
        print(self.a)
        print(self._a)
        print(self.__a)
    
    def __func(self):
        print(self.a)
        print(self._a)
        print(self.__a)
        
class B(A):
    def __init__(self):
        A.__init__(self)
```

实例化一个A的对象：

```python
duixiang = A();
print(duixiang.a)# 1 
print(duixiang._a)# 2
# duixiang.__a # AttributeError: 'A' object has no attribute '__a'

duixiang.func()# 1 2 3
duixiang._func()# 1 2 3
# duixiang.__func()# AttributeError: 'A' object has no attribute '__func'
```
运行结果截图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730002255162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VsYWluZV9EV0w=,size_16,color_FFFFFF,t_70)
上述结果说明：
* 类的成员函数（无论哪种命名）都可以对该类的所有类型变量进行访问；
* 类的对象可以对（`var`，`_var`）这两类变量直接访问，但是无法访问`__var`这类变量；
* 类的对象可以对（`func()`，`_func()`）这两类函数直接访问，但是无法访问`__func()`这类函数。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730002333562.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730002904711.png)
---
B是A的子类，实例化一个B的对象：

```python
duixiang2 = B()
print(duixiang2.a)# 1
print(duixiang2._a)# 2
# print(son.__a)# AttributeError: 'B' object has no attribute '__a'

duixiang2.func()# 1 2 3
duixiang2._func()# 1 2 3
# son.__func() # AttributeError: 'B' object has no attribute '__func'  
```
运行结果截图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730003026138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VsYWluZV9EV0w=,size_16,color_FFFFFF,t_70)
上述结果说明：
* 类的子类的对象只能对（`var`，`_var`）这两类变量直接访问，但是无法访问`__var`这类变量；
* 类的子类的对象只能对（`func()`，`_func()`）这两类函数直接访问，但是无法访问`__func()`这类函数。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730003207526.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730003239534.png)

## 参考

[博客1](https://www.cnblogs.com/ybf-yyj/p/9026588.html) 