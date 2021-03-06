---
layout: post
title: c++中类的拷贝控制操作
date: 2018-10-16 10:07:24.000000000 +09:00
img:  cartoon2.jpg # Add image post (optional)
tag: [c与c++]
---
通常一个类的拷贝控制操作由以下三个函数来定义：
1. 拷贝构造函数

2. 拷贝赋值运算符

3. 析构函数

以上这３个成员，如果在一个类中未自己定义时，编译器会为这个类自动生成默认的版本。三个成员共同构成了一个类基本的的拷贝控制与资源管理操作。

## `=default` 与 `=delete` 的使用
我们可以通过将拷贝控制成员定义为`=default`来**显式**地要求编译器生成合成版本的。

在c++11标准以后我们可以通过将拷贝构造函数与拷贝赋值运算符定义为`=delete`来**组织拷贝的发生**。`=delete`告诉编译器，我们不希望定义这些成员。例如，`iostream`类阻止了拷贝操作。**析构函数不能是`=delete`的**，如果我们用`=delete`将一个类的析构函数定义为删除的，则我们不能定义该类型的变量（但是可以动态分配内存），也不能释放指向该类型动态分配对象的指针。

## 拷贝构造函数
拷贝构造函数也是一种构造函数。我们知道，不同版本的构造函数的区别在于参数列表的不同。**拷贝构造函数的第一个参数一定是自身类类型的引用**，任何额外参数都有默认值。

如果一个类未定义自己的拷贝构造函数，则编译器会为其自动生成一个，自动生成的拷贝构造函数就是将类中的每一个数据成员进行简单的拷贝构造。如果不希望自己的类有拷贝构造函数操作，必须显式将拷贝构造函数声明成`=delete`来指出我们希望将它定义成删除的，比如`iostream`类就阻止了拷贝操作来避免多个对象写入或者读取相同的IO操作。

此处编写一个简陋的`HasPtr`类为例实现**深拷贝**，比较直观有代码感受：
```cpp
class HasPtr
{
public:
	HasPtr(const string &s = string()) : ps(new std::string(s)) {cout<<"调用HasPtr()"<<endl;}

	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)) {cout<<"调用HasPtr(HasPtr&)"<<endl;}

	~HasPtr(){delete ps;}

private:
	std::string *ps;
};

```
***
如上，这个类有两个版本的构造函数，其中第一个是普通构造函数（可以没有参数，也可以有一个`string&`类型），第二个是拷贝构造函数（参数是自身类型的引用）。

> 注意：拷贝构造函数的第一个参数之所以是引用类型，是因为在**函数调用过程中，具有非引用类型的参数要进行拷贝初始化**。如果拷贝构造函数的参数不是引用类型的话，则会对拷贝构造函数的形参进行拷贝初始化，会再一次调用拷贝构造函数，再对这个拷贝构造函数的形参进行拷贝初始化....如此往复，无限循环。

## 拷贝赋值运算符
拷贝赋值运算符其实就是重载了`=`，如果类未定义自己的拷贝赋值运算符，编译器会为它合成一个，合成的拷贝赋值运算符也就是简单的对类的每个数据成员进行简单的拷贝赋值。如果不希望自己的类有拷贝构造函数操作，必须显式将拷贝构造函数声明成`=delete`来指出我们希望将它定义成删除的，比如`iostream`类就阻止了拷贝操作来避免多个对象写入或者读取相同的IO操作。继续上一个`HasPtr`，为其添加拷贝赋值运算符；
```cpp
class HasPtr
{
public:
	HasPtr(const string &s = string()) : ps(new std::string(s)) {cout<<"调用HasPtr()"<<endl;}

	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)) {cout<<"调用HasPtr(HasPtr&)"<<endl;}

	HasPtr& operator=(const HasPtr& hp){
		cout<<"调用operator="<<endl;
		auto new_ptr = new std::string(*hp.ps);	//防止对自身进行赋值，不能先释放ps指向的内存
		delete ps;
		ps = new_ptr;
		return *this;
	}

	~HasPtr(){delete ps;}

private:
	std::string *ps;
};
```
***
## 析构函数
因为一个对象通常在自己的作用域结束后会自动销毁，它销毁的方式就是挨个销毁自己的数据成员。但是有时**光销毁自己的数据成员**是不够的，比如说当此类的数据成员中有一个指向一块动态分配来的内存的指针。此时光销毁指针显然是不够的。我们还需要在销毁指针之前将指针指向的这块动态内存给释放了。而析构函数就是做这个工作的。其中成员的销毁是隐式进行的。

> 析构函数负责在销毁对象的数据成员前完成料理好它们的**身后事**。使得即使这个对象的数据成员被销毁了，依然不会有乱子出现。

## 三/五法则（自己翻书把）

## 探究初始化操作与赋值操作真正调用的函数
使用上述定义的类，对它进行测试：
```cpp
int main(){
	HasPtr  a("hello wprld");  
	cout<<endl;

	HasPtr b(a);    
	cout<<endl;

	HasPtr c = a;
	cout<<endl;

	string s = "hello";
	HasPtr d = s;
	cout<<endl;

	HasPtr e;
	e=a;
 

    return 0;
}
```
*** 
上述程序运行后，输出如下：
```bash
xhy@ubuntu:~/cpp_learn/ch13$ ./test_init 
调用HasPtr()

调用HasPtr(HasPtr&)

调用HasPtr(HasPtr&)

调用HasPtr()

调用HasPtr()
调用operator=
```
***　
> 由上可知，只要是**初始化操作**，无论是直接初始化（使用参数表）还是拷贝初始化（使用＝），全部使用的是构造函数，具体使用哪个版本的构造函数则由括号或者＝号提供的参数形式决定。只有当是普通的赋值操作时，才使用拷贝赋值运算符（重载的＝）。

### 加强版测试
```cpp
#include <iostream>
#include <vector>
#include <initializer_list>

struct X
{
	X() { std::cout << "X()" << std::endl; }
	X(const X&) { std::cout << "X(const X&)" << std::endl; }
	X& operator=(const X&) { std::cout << "X& operator=(const X&)" << std::endl; return *this; }
	~X() { std::cout << "~X()" << std::endl; }
};

void f(const X &rx, X x)
{
	std::vector<X> vec;
	vec.reserve(2);
	vec.push_back(rx);
	vec.push_back(x);
}

int main()
{
	X *px = new X;
	f(*px, *px);
    X x;
    x = *px;
	delete px;

	return 0;
}
```
***
输出结果如下：
```bash
xhy@ubuntu:~/cpp_learn/ch13$ ./test_all 
X()
X(const X&)
X(const X&)
X(const X&)
~X()
~X()
~X()
X()
X& operator=(const X&)
~X()
~X()
```
***
如上，测试函数说明了：
1. 在函数调用时，**不是引用类型的形参都是采用拷贝构造函数**进行形参的初始化的，引用类型的形参直接绑定到实参上。

2. 在`vector`进行`reserve`函数进行空间分配时，只是分配了空间，并没有在分配的空间上进行初始化操作。

3. 在`vector`进行`push_back`操作时，使用拷贝初始化函数对新增元素进行初始化操作；

4. 在一个函数作用域结束时，会将此**作用域中的自动变量**一一析构（包括形参）。


# 参考资料
- [1] C++ Primer（第5版）



