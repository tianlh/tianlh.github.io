---
layout: post
title: "泛型编程 C++"
date: 2020-9-13
author: Tianlh
header-img: "img/head-bg1.jpg"
tags:
  - C++
  - 模板
---

#### **函数模版**
```c++
template <模板形参列表>
[函数返回类型] [函数名] (函数参数列表)
{
    函数体
}
template <typename T1, typename T2, ...>
void fun(T1 t1, T2 t2)
{
    函数体
}
```
- T为模版形参
- 模板形参列表中可以有非类型模板形参(如 int i)，也就是普通的内置类型形参
- 非类型模板形参在模板内时常量值，实例化时对应实参须为常量表达式
- 非类型模板形参只能是整形，指针或引用
- 如局部对象的地址都不是常量表达式，double不是整形，都不能作为非类型模板实参
- 全局对象地址或引用，引用const类型对象（如 const double&），const整形变量，sizeof的结果等是常量表达式，可以作为非类型模板实参
- 函数中使用内置类型的地方可以使用模板形参代替
- 使用fun(2, 3.0)，编译器根据实参推演出函数模板的实参类型，从而实例化一个函数模板
- 也可以通过fun<int, double> fun(2, 3.0)这样的形式显示调用

#### **类模板**
```c++
template<模板形参列表>
class [类名]
{
    类主体
}
template<typename T1, typename T2, ...>
class A
{   
    T1 m;
public:
    void f(T1 t1, T2 t2);
}

template<typename T1, typename T2, ...>
void A<T1, T2> fun()
{
    函数主体
}
```

- 类中使用内置类型的地方可以用模板形参来代替
- 类外定义成员函数时时template后的模板形参与类的模板形参一致
- 模板声明和定义只能在全局空间、命名空间或类作用域内进行
- 通过A<int， double> a的形式实例化，类模板形参实例化不能通过实参推演得到

#### **实例化**

##### 显示实例化
- 指在不调用函数或者不使用类模板的时候进行实例化
- 函数模板显示实例化
<br/>`template [函数返回类型] [函数模板名]<实际参数列表>(函数参数列表)`
<br/>`template void fun<int, double>(int, double);`
- 类模板显示实例化
<br/>`template class [类名]<实际参数列表>`
<br/>`template class A<int, double>;`

##### 隐式实例化
- 函数模板隐式实例化
<br/>调用时不显示给出模板参数，编译器通过实参推演实例化函数模板
<br/>`fun(1, 2.0);`
- 类模板隐式实例化
<br/>使用时才将类模板实例化，实例化后叫模板类`A<int, double> a;`

##### 隐式模板实参调用
- 只针对函数模板，不给出模板参数，通过实参推演实例化函数模板

##### 显示模板实参调用
- 函数模板调用时，显示给出模板参数
<br/>`fun<int, double>(1, 2.0);`

#### **模板全特化**
```c++
template<> [返回类型] [函数名]<特化类型列表>(参数列表)
{
    函数体
}
template<> void fun<int*, double*>(int* t1, double* t2)
{
    函数体
}

template<>
class [类名]<特化类型列表>
{
    类主体
}
template<>
class A<int*, double*>
{
    类主体
}
```
- 定义函数模板在实例化为特定类型时的实现版本
- 将所有模板类型都进行特化
- 重载函数也能达到同样效果，但是
<br/>使用重载函数不管实际是否调用都会生成二进制代码，而使用特化只有实际调用才会生成二进制代码
<br/>使用重载函数，需要在各个源文件中包含重载函数的声明，否则某些源文件仍会使用模板函数
- 当有可以直接匹配的函数时，优先使用直接匹配的函数，而不是特化的函数

#### **模板偏特化**
- 只有类模板可以偏特化
- 函数模板只能全特化，因为偏特化可以由重载实现
```c++
template<未特化的模板形参列表>
class [类名]<特化类型列表>
{
    类主体
}
template<typename T1>
class A<double*>
{
    类主体
}
```
