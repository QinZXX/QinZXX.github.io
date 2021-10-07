---
layout: post
title: 关于C++语法解析遇到的一个问题
categories: ConcurrencyProgramming, CPlusPlus
description: 关于C++语法解析遇到的一个问题
keywords: 并发, Concurrency, 语法解析
---



# C++： most vexing parse（语法解析）


## 令人头痛的语法解析
(这样的问题感觉很难发现，尤其在项目相对复杂的时候。。平时应该如何避免呢)

在写C++的时候遇到一个问题，初始化一个对象的时候构造函数没有被调用。类似的代码如下：

```cpp
#include <iostream>
#include <string>
using std::cout;
using std::endl;

class A {  
public:
    A(int num){
    	m_num=num;
        cout << "构造函数执行"<< endl;
    }
	A(){
		m_num=0;
		cout<<"默认构造函数执行"<<endl;
	}
	A(std::string s){
		num=s.size();
		cout<<"string形参构造函数"<<endl;
	}
	
private:
	int m_num;
};
	
int main()  {
    A a();//被当成函数声明

int num=99;
A b(int (num));//被当成函数声明

char stemp[]="hello";
A c(std::string(stemp));//被当成函数声明

return 0;
```


这段代码中，（最后一个例子）意图是构造一个std::string型匿名对象，然后传递给类A的构造函数，构造函数输出这个字符串。但是这段代码什么都没输出，说明**构造函数没有被执行**。这个奇怪的结果让我很好奇。
第一个和第二个例子也没有执行相应的构造函数，why？

经过一番资料查阅，原来A a(std::string(strmp));这段代码被解析成了一个**函数名为a的有一个std::string参数stemp并返回A类型的函数声明**。这在Scott Meyers的《Effective STL》有做解释，并把这个问题称为C++'s most vexing parse,本文也用了这个标题，翻译为C++最令人费解的解析。

在C++中，以下三种写法都声明了同一个函数

```cpp
int f(double d); //声明接受一个double参数d，返回值为int类型的函数  
int f(double (d));//效果一样，参数名外的括号会被忽略  
int f(double);//直接省略参数名  
```


类似的，以下三种写法都声明的函数也相同

```cpp
int g(double (*pf)()); //声明接受一个无参数返回类型为double的函数指针pf参数，
//返回值为int类型的函数  
int g(double pf());//效果一样，pf是隐式函数指针  
int g(double ();；//直接省略参数名 
```


前面代码中的A a(std::string(stemp));其实就跟函数f的第二种声明方式，stemp两边的括号被忽略。然后被解析成一个函数声明。还有一种情况，如果这段这段代码改成A a();,也不会调用A的默认构造函数，同样会被解析成函数声明。

## 关于函数声明的位置
1.在调用的函数前定义函数，此时可以不需要声明
2.在调用的函数前声明
3.在调用的函数里面也可以声明
4.在其他文件的头文件*.h文件里面声明，然后*.c文件直接调用头文件也可以。
以上，static函数慎用（它只在定义声明 它的文件中可见，而普通函数默认是extern的）

————————————————

1.函数在使用之前要声明
当函数定义放在main函数之后时（或者其他.cpp文件都可以）（关于编译的规则），函数声明可以在main函数之前，也可以在main函数里面（只要在（首次）调用此函数的语句之前的任意位置处声明都可以，一般都在main函数开头处声明）
2.当函数定义在main函数之前时，main函数里面就不用再次声明了，直接调用即可。
3.当函数定义的函数体比较长的时候，一般把定义写在main函数之后，声明写在main函数里面或者前面。

————————————————

所以。函数声明可以在main里面，也可以在main外面。

可以查看我另一篇有相关内容的文章：
https://blog.csdn.net/qq_26189301/article/details/102699627

## 贱大佬的解释
问了下爱吃烤鱼和炸鸡的贱大佬，大佬说：

**1.为了兼容C语言，C++里规定，一切可以被当成函数声明的都会被当成函数声明。**

**2.如果要使用一个类的话。如果这个类的构造函数里没有参数，就初始化时就把括号去掉，比如这个例子中，把A a();改成A a;就不会被当成函数声明而是当成默认构造函数了；**

如果这个类的构造函数里带了参数，那么就要把这些参数加进去。

像这里这样的情况，参数是用的该参数类型的构造函数创建的匿名对象传进去的，但是这样就会把std::string(stemp)解析成 std::string stemp，即函数名为a的带有一个形参且该形参类型为std::string的一个stemp形参的函数声明。
**（直接传递进stemp（用隐式转换）就不会有歧义，就会调用构造函数）**

## 解决方案
这是一个违反直觉的解析方式，所以在C++11中，针对这种情况，有提出解决方案。

1.Scott在书中有提到一种解决方法，就是**把整个匿名对象用括号括起来，就像这样A a((std::string(stemp)));**。

2.**更好地做法还是避免写这样的代码，而是先在外面初始化一个std::string类型的变量，然后再传给构造函数。或者直接通过隐式转换A a(stemp);来创建对象**。

在C++11中，使用Uniform initialization可以处理这种歧义，

> Uniform initialization syntax Using the new uniform initialization
> syntax introduced in C++11 solves this issue.
>
> The problematic code is then unambiguous when braces are used:
>
> TimeKeeper time_keeper{Timer{}};
>

3.**使用新的语法可以这样写`A a{std::string(stemp)};`**

```cpp
class A{
public:
    A(){
        m_cao = 100;
        cout << "Default constructor" << endl;      
    }
    A(int caohongjian) {
        m_cao = caohongjian;
        cout << "constructor" << endl;
    }
private:
    int m_cao;
}

int main()
{
    int t1 = 99;
    double t2 = 9.9;
    A a();
    a();
    A b(t1);
    A c;
    
    A d(int(t2));
    d(int(t2));
    
    return 0;
}

A a()
{
    cout << "be treated as function's  declaration"  << endl;
}

A d(int h)
{
    cout << "d function" << endl;   
}
```

result:

```bash
be treated as function's  declaration
constructor
Default constructor
d function
```



## 创建线程时的问题
定义一个函数对象（仿函数）（重载了()运算符的类）:

```cpp
class background_task{
public:
  void operator()() const
  {
    do_something();
    do_something_else();
  }
};

background_task f;
std::thread my_thread(f);
```

当把函数对象传入到**线程构造函数**中时，需要避免“最令人头痛的语法解析”(C++’s most vexing parse)。如果你传递了一个临时变量(匿名变量)，而不是一个命名的变量；C++编译器会将其解析为**函数声明**，而**不是类型对象的定义**。

例如：

```cpp
std::thread my_thread(background_task());
```

**这里相当与声明了一个名为my_thread的函数，这个函数带有一个参数(函数指针指向没有参数并返回background_task对象的函数)，返回一个std::thread对象的函数，而非启动了一个线程**。


下面这一段？（当成声明了）

```cpp
class background_task{
public:
  void operator()() const
  {
    do_something();
    do_something_else();
  }
};

std::thread my_thread(back_ground);
```

![imgcode](/images/posts/cplusplus/code.png)

result:

```bash
调用函数
:terminal called without an active exception
```

也就是上述提到的下图中的第三种省略参数名的函数声明方式：

```cpp
int g(double (*pf)()); // 声明接受一个无参数，返回类型为double的函数指针pf参数
// 返回类型为int类型的函数
int g(double pf()); // 效果一样，pf是隐式函数指针
int g(double ()); // 直接省略函数明
```

前面代码中的`A a(std::string(stemp)`，其实就跟函数f的第二种声明方式同，stemp两边的括号被忽略。然后被解析成一个函数声明。还有一种情况，假设这段代码改成`A a()`，也不会调用A的默认构造函数，也会被解析成函数声明。



使用在前面命名函数对象的方式，或使用多组括号①，或使用新统一的初始化语法②，可以避免这个问题。

如下所示：

```cpp
std::thread my_thread((background_task()));  // 1
std::thread my_thread{background_task()};    // 2
```


使用lambda表达式也能避免这个问题。lambda表达式是C++11的一个新特性，它允许使用一个可以捕获局部变量的局部函数(可以避免传递参数)。之前的例子可以改写为lambda表达式的类型：

```cpp
std::thread my_thread([]{
  do_something();
  do_something_else();
});
```