---
layout: default
category: cplusplus
---

> C++类的内存分布开个头吧

- 对于类来说，最简单的就是Empty类

```C++
class Empty {	};

std::cout<<sizeof(Empty)<<std::endl; // 输出1
// PS: C++中sizeof算是operator而非function
```

也就是说，空类的大小为1，之所以不为0，就是为了保证不同对象的位置（address）不同，而大小为1恰恰就等于sizeof(char)。

也就是说类的大小还是有可能为0的，即：

```C++
class Empty { 
	int a[];
};

std::cout<<sizeof(Empty)<<std::endl; // 输出0
```

当然，说是这么说，实际上现在的编译器，如g++，clang++，尽管是size为0的类，他们的地址都是不同的（已在ubuntu下g++,clang++，windows下visual studio 2015测试过）。

- 另外，Empty类也是有它的作用的。Effective C++条款39提到EBO技术（Empty Base Object），Empty类中可以有大量的enum，typdefs可以使用，通常用private继承（不占内存）或是进行组合（会因为内存对齐而占多于1的内存），而为了易于理解，尽管内存稍微用多一点，还是推荐进行组合。

---

- 然后到考虑内存分布，首先如果类中有虚函数，那么会在第一个位置放入虚函数指针，然后就是类中成员按顺序从低地址到高地址依次排列。

```C++
class Base {
private:
	static int num;
	char ch;
	int number;
public:
	virtual int getNumber() {
		return number;
	}
	virtual ~Base() {}
};


// 在Visual Studio中启用显示类内存分布，一目了然
// 启用方法：配置->C/C++->命令行->添加/d1 reportSingleClassLayoutXXX （XXX为类名，实际上VS是使用正则表达式来进行匹配，因此可能会出现其它类
/*
  class Base	size(12):
  	+---
    0	| {vfptr}
    4	| ch 
    	| <alignment member> (size=3)
    8	| number
  	+---
*/

/*
  Base::$vftable@:
        | &Base_meta
        |  0
   0	| &Base::getNumber
   1	| &Base::{dtor}
*/
```

0. static成员不占对象空间，而占静态内存空间
1. 32位程序（上面注释部分）：sizeof(Base) = sizeof(vfptr)(4) + sizeof(char) + <alignment member> (对齐vfptr) + sizeof(int) = 12
2. 64位程序，由于指针大小变为8bytes，因此大小变为16。
3. 虚表不占用对象内存，只是代码段中定义的位置，dtor为destructor。
4. 类普通股函数与虚函数的不同就在于，普通函数直接在编译阶段就可以确定调用的函数的位置，而虚函数需要在运行期间，通过查询虚函数表(vftable)才能正确的调用。
5. 因此，对于非final的类，即有可能被继承的类，析构函数应为虚函数，才能正确的析构，否则将有可能出现内存泄漏。
6. 构造函数显然不可能是虚函数，因为类构造之前不存在vftable，也就是会找不到构造函数定义的位置，使得无法构造。
7. 初始化顺序，根据内存分布，自然也是由上到下，按变量的定义顺序来安排。

```C++
// Base 就是上一个Base
class Derived: public Base {
private:
	char ch2;
	int number;  // 另一个number其实也不会报错，因为这是Derived::number，那一个是Base::number
};

/*
  class Derived	size(16):
  	+---
    0	| +--- (base class Base)
    0	| | {vfptr}
    4	| | ch
     	| | <alignment member> (size=3)
    8	| | number
      	| +---
    12	| number
*/

/*
  Derived::$vftable@:
        | &Derived_meta
        |  0
    0	| &Base::getNumber
    1	| &Derived::{dtor}
*/
```

1. 类继承会原封不动，把Base class的拿过来，包括虚函数表指针。

那么对于多重继承，结果如下：

```C++
class Base {
private:
	static int num;
	char ch;
	int number;
public:
	Base() {
		cout << "Base constructor" << endl;
	}

	virtual int getNumber() {
		return number;
	}
	virtual ~Base() {
		cout << "Base destructor" << endl;
	}
};

class AnotherBase {
public:
	AnotherBase() {
		cout << "Another constructor" << endl;
	}
	virtual ~AnotherBase() { 
		cout << "Another destructor" << endl;
	}
};

class Derived: public Base, public AnotherBase {
private:
	char ch2;
	int number;
public:
	Derived() {
		cout << "Derived constructor" << endl;
	}

	~Derived() {
		cout << "Derived destructor" << endl;
	}
};

/*
  class Derived	size(24):
	  	+---
   0	| +--- (base class Base)
   0	| | {vfptr}
   4	| | ch
    	| | <alignment member> (size=3)
   8	| | number
	  	| +---
   12	| +--- (base class AnotherBase)
   12	| | {vfptr}
 	  	| +---
   16	| ch2
    	| <alignment member> (size=3)
   20	| number
	  	+---

	  Derived::$vftable@Base@:
        | &Derived_meta
        |  0
   0	| &Base::getNumber
   1	| &Derived::{dtor}

	  Derived::$vftable@AnotherBase@:
        | -12
   0	| &thunk: this-=12; goto Derived::{dtor}
*/

/* output
Base constructor
Another constructor
Derived constructor
Derived destructor
Another destructor
Base destructor
*/
```

1. 对于两个虚析构函数可以看到，AnotherBase的析构函数，会被直接跳转到第一个继承的Base中的析构函数中

--- 
下面陆续补充笔试面试题目