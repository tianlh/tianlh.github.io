---
layout: post
title: "Effective C++"
date: 2020-8-22
author: Tianlh
header-img: "img/head-bg1.jpg"
tags:
  - C++
  - 编码安全
---

为了考公司的认证，把C++基础重新看一遍，巩固一下也不错。
##### **尽量以const，enum，inline替换#define**
##### **尽可能使用const**
- `T const * p`等同与`const T * p`表示指针指向的值不可变
<br/>`T * const p`，指针指向不可变，迭代器同理
- `int A::fun () const` 成员函数声明和定义后都加const
<br/>表示该成员函数内不可修改对象内non-static成员变量
<br/>mutable T var：使用关键词mutable定义成员变量使其在const成员函数中也可被修改
  
##### **对象使用前确保初始化**
- `A::A(T a, T b) : var1(a), var2(b) {}`：优先使用成员初值列进行初始化
- 基类比派生类先初始化
- class成员变量按声明次序初始化
- 跨编译单元的non-local static对象初始化顺序无明确定义，因此使用local static对象替代non-local static对象（如singleton模式）可以避免出现初始化次序问题

##### **C++默认生成函数**
- C++在以下函数调用且合法时才创建
```c++
class A {
public:
    A() { ... }               // default构造函数
    A(const A &a) { ... }     // copy构造函数
    ~A() { ... }              // 析构函数
    A& operator= (const A &a) { ... }    // copy assignment函数
}
```
- 可以通过将相映成员函数声明为private，并且不实现它来避免编译器自动生成默认函数

##### **需为多态基类声明virtual析构函数**
- 最深层次的派生类先析构，最后基类析构
- 定义虚函数时编译器为每个带有虚函数的类创建一个vtbl（virtual table），用于保存对应类的虚函数地址。并且为每个类增加一个vptr（virtual table pointer）指向vtbl首地址。
<br/>定义派生类对象时，先执行基类构造函数，此时s.vptr指向基类vtbl。随后执行派生类构造函数，s.vptr指向派生类vtbl。所以父类构造函数中调用虚成员函数无法实现多态。
![image]({{ "/img/post/265090062cd4200bf872457a11f65d81.png" }})
- 虚继承：主要用于解决菱形继承问题
<br/>虚继承在派生类中只保留一份共有基类的b拷贝，但是每个虚继承的基类都增加了vbptr（virtual base table pointer），指向了虚基类表（virtual table），虚基类表记录公共基类的成员变量距离该类开始处的offset
![image]({{ "/img/post/2edfcba94bb41fdc9fe5c9489678de40.png" }})

##### **不在构造和析构过程中调用虚函数**

##### **建议operator=**
- 返回一个reference to *this，以便实现连锁赋值
- 注意处理自赋值场景
- 建议使用copy-and-swap技术，先new再delete，避免new异常时赋值失败，并且原有对象也已经被delete
- 复制所有local成员，并且记得调用基类copy函数复制基类成员

##### **RAII，资源获取就是初始化**
- 使用智能指针管理资源，获得资源后立刻放进管理对象内
- auto_ptr：对auto_ptr进行赋值或者拷贝构造，被拷贝内容会被置为null，因此不安全
```c++
int *p=new int(0);
auto_ptr<int> ap(p);
```
- unique_ptr：类auto_ptr，并且禁止赋值与拷贝
```c++
unique_ptr<T> u;       // 创建空智能指针
u.reset(new T());      // 绑定对象  
unique_ptr<T,D> u(d);    // 创建空unique_ptr，执行类型为T的对象，用类型为D的对象d来替代默认的删除器delete
T *p = u.release();    // 释放所有权  
unique_ptr<T> u(new T());  
unique_ptr<T> u2 = std::move(u); // 转移所有权，u变成“空指针” 
u2.reset(u.release());    // 所有权转移
u2 = nullptr;  // 显式销毁所指对象，同时u2变为空指针。与u2.reset()等价
```
- share_ptr：采用引用计数u2，计数减至0释放
```c++
shared_ptr<T> u(static_cast<T*>(0), d);  // 使用d替代默认的删除器
```
- weak_ptr：将share_ptr的对象赋值给weak_ptr，不会改变引用计数，避免share_ptr循环引用问题
- new对象和智能指针绑定对象分开为独立语句，避免执行顺序引起的内存泄露

##### **以pass-by-reference-to-const替换pass-by-value**
- 内置类型以及STL的q迭代器和函数对象依然建议用pass-by-value

##### **不要返回pointer或reference指向local对象**

##### **以non-member、non-friend替换menber函数**
- non-member、non-friend不能增加“能访问class内private成分”的函数数量，所以具有更好封装性
- 如果需要对函数所有参数（包括被this指针所指的隐喻参数）进行类型转换，必须使用non-member，避免隐式类形状换带来的异常调用

##### **少做转型动作**
- 使用const_cast、dynamic_cast、reinterpret_cast、static_cast
<br/>dynamic_cast 向下安全转型，会做动态类型检查，效率较低，通常只用于derived class对象上执行它的成员函数，但是只有一个base class的指针时。

##### **避免返回handles（指针、引用、迭代器等）指向对象内部**

##### **inline**
- inline关键词放在函数定义前
- 将函数定义于class内也是种inline
- 不建议将构造函数和析构定义为inline，因为他们往往有编译器产生的一些代码，并不是小型函数
- inline函数的修改会导致所有使用它的代码块重新编译

##### **将文件编译依赖降至最低**
- 使用handle class 和 interface class，如工厂模式
- 头文件完全且仅有声明式

##### **避免遮掩继承而来的名称**
- 重载，同一个类内，同名函数不同参数列表，不关心返回值类型
- 重写（override），基类声明virtual函数，派生类实现函数名、参数列表、返回值完全一致的函数
- 隐藏，派生类定义基类同名函数，在派生类中就访问不了基类的同名函数了
- 使用`using Base::f();`声明可以让基类被隐藏的函数在派生类中可见。
- public继承不改变基类访问权限
<br/>protected继承，基类public类型转为protected，其他不变
<br/>private继承，基类所有访问权限为private，子类无权访问
<br/>private继承时有两种准许访问机制：使用`using Base::f();`或者用`Base::f()`这样的访问声明（只能恢复原有权限，不能拔高权限）

##### **区分接口继承和实现继承**
- pure virtual函数可以拥有自己的实现，实现接口继承和缺省实现继承
- 不要重新定义继承而来的non-virtual函数

##### **不要重新定义继承的缺省参数值**
- non-virtual不应该重新定义
- virtual是动态绑定，但缺省函数值是静态绑定

##### **定制new和delete**
- set_new_handler允许用户指定一个函数，在new内存失败时调用
```c++
namespace std {
    typedef void (* new_handler)();
    new_handler set_new_handler(new_handler p) throw();
}
``` 
- 在必要时可以重载new和delete，以改善性能，收集调试信息等
- operator new含一个无穷循环，在其中尝试分配内存，分配失败时调用new_handler。它也应该正确处理0 bytes申请
- operator delete应该在收到null指针时不做任何处理
- operator new和operator delete的placement版本指接受除`std::size_t`以外，还有额外参数的版本，placcement new和delete需要对应

##### **标准程序库**
- stl、iostreams、locales
