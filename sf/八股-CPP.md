

[TOC]



# 1.  decltype总结

 https://www.cnblogs.com/ghbjimmy/p/10636030.html 
decltype和auto都可以用来推断类型，但是二者有几处明显的差异：

1.  auto忽略顶层const，decltype保留顶层const；
2.  对引用操作，auto推断出原有类型，decltype推断出引用；
3.  对解引用操作，auto推断出原有类型，decltype推断出引用；
4.  auto推断时会实际执行，decltype不会执行，只做分析。总之在使用中过程中和const、引用和指针结合时需要特别小心。



------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 2. Lambda 语法分析

https://www.cnblogs.com/jimodetiantang/p/9016826.html

## ![img](https://images2018.cnblogs.com/blog/1382048/201805/1382048-20180509221109509-1786172899.jpg)2.1 [函数对象参数]

标识一个 Lambda 表达式的开始，这部分必须存在，不能省略。函数对象参数是传递给编译器自动生成的函数对象类的构造
函数的。函数对象参数只能使用那些到定义 Lambda 为止时 Lambda 所在作用范围内可见的局部变量(包括 Lambda 所在类
的 this)。函数对象参数有以下形式：

-   空。没有任何函数对象参数。
-   =。函数体内可以使用 Lambda 所在范围内所有可见的局部变量（包括 Lambda 所在类的 this），并且是值传递方式（相
    当于编译器自动为我们按值传递了所有局部变量）。
-   &。函数体内可以使用 Lambda 所在范围内所有可见的局部变量（包括 Lambda 所在类的 this），并且是引用传递方式
    （相当于是编译器自动为我们按引用传递了所有局部变量）。
-   this。函数体内可以使用 Lambda 所在类中的成员变量。
-   a。将 a 按值进行传递。按值进行传递时，函数体内不能修改传递进来的 a 的拷贝，因为默认情况下函数是 const 的，要
    修改传递进来的拷贝，可以添加 mutable 修饰符。
-   &a。将 a 按引用进行传递。
-   a，&b。将 a 按值传递，b 按引用进行传递。
-   =，&a，&b。除 a 和 b 按引用进行传递外，其他参数都按值进行传递。
-   &，a，b。除 a 和 b 按值进行传递外，其他参数都按引用进行传递。

## 2.2 (操作符重载函数参数)

标识重载的 () 操作符的参数，没有参数时，这部分可以省略。参数可以通过按值（如: (a, b)）和按引用 (如: (&a, &b)) 两种
方式进行传递。

## 2.3 mutable 或 exception 声明

这部分可以省略。按值传递函数对象参数时，加上 mutable 修饰符后，可以修改传递进来的拷贝（注意是能修改拷贝，而不是
值本身）。exception 声明用于指定函数抛出的异常，如抛出整数类型的异常，可以使用 throw(int)。

## 2.4 -> 返回值类型

标识函数返回值的类型，当返回值为 void，或者函数体中只有一处 return 的地方（此时编译器可以自动推断出返回值类型）
时，这部分可以省略。

## 2.5 {函数体}

标识函数的实现，这部分不能省略，但函数体可以为空。

```
int main()
{
    vector<int> arr = {3, 4, 76, 12, 54, 90, 34};
    sort(arr.begin(), arr.end(), [](int a, int b) { return a > b; }); // 降序排序
    for (auto a : arr)
    {
        cout << a << " ";
    }
    return 0;
}
```



---

# 3.  范围for语句

https://www.zhihu.com/question/438300953/answer/1667885150
要知道为什么要写引用，我们就要首先知道基于范围的for循环（range-*based-*for）是什么，C++17标准里是这样定义range-*based-*for的：  

```cpp
for ( for-range-declaration : for-range-initializer ) statement
```

会被编译器解释为下面的代码：  

```text
{
	auto &&__range = for-range-initializer;
	auto __begin = begin-expr;
	auto __end = end-expr;
	for (; __begin != __end; ++__begin) {
		for-range-declaration = *__begin;
		statement
	}
}
```

那么，你的代码（我们假设你的test_string是::std::string类型），那么就等价于：  

```text
{
	auto &&__range = test_string;  //此处auto被推导为::std::string&类型，根据引用的折叠规则，::std::string&&&被折叠为::std:::string&
	auto __begin = __range.begin();  //__begin被推导为::std::string::iterator
	auto __end = __range.end();
	for (; __begin != __end; ++__begin) {
		auto c = *__begin;  //此处auto被推导为char，即char c = *__begin
		cout << c << endl;
	}
}
```

也就是说，这里是把test_*string里的字符复制了一份给c，然后你输出的是复制过来的c的值。这样写当然是没有问题的。但是，如果你想要在循环体里改变test_*string中的元素，这就不行了，因为你对c的任何操作，都是对c这个变量副本进行操作，并没有真的对test_*string里的元素进行操作。因此，如果要改变test_*string内的元素，就需要把c定义成引用变量来引用test_*string中的元素。这时候，对c的操作就是对test_*string内的元素进行操作了。




---
# 4. constexpr
constexpr 定义编译器确定的数值或者运算，const只保证变量不可变，并不保证数值是编译器运算的

const修饰的变量可以被常量表达式初始化（这时const变量可以用于指定数组大小），也可以被编译期不能计算出值的表达式初始化，比如大部分函数的返回值（这时const变量，不能用于指定数组大小）。

```
const int d = fun( void ); //不是常量
```

constexpr修饰的变量，一定要用常量表达式初始化，一定是常量（严格说，const还是不能被修改的变量，constexpr已经可以丧失变量概念了），一定可以用于指定数组的大小。

---
# 5. final, override
类被final修饰，不能被继承

```
class A1 final { };
class B1 : A1 { }; // “B1”: 无法从“A1”继承，因为它已被声明为“final”
```



虚函数被final修饰，不能被override

```
class A1
{
	virtual void func() final {} 
};

class B1 : A1
{
	virtual void func() {} //“A1::func”: 声明为“final”的函数无法被“B1::func”重写
};
```


被override修饰后如果父类无对应的虚函数则报错，无法override,这个有什么作用呢，假如你想虚继承基类的函数，但是继承的时候写错了，参数类型不对或个数不对，但是编译没问题，运行时候却和你设计的不一样不被调用，override就是编译器辅助你检查是否继承了想要虚继承的函数

```
struct A1
{
	virtual void func(int) {}
};

struct B1 : A1
{
	virtual void func(int) override {} // OK
	virtual void func(double) override {} // “B1::func”: 包含重写说明符“override”的方法没有重写任何基类方法
}
```


---
# 6. 面向对象的三大特性
封装：将具体的实现过程和数据封装成一个函数，只能通过接口进行访问，降低耦合性。

继承：子类继承父类的特征和行为，子类有父类的非 private 方法或成员变量，子类可以对父类的方法进行重写，增强了类之间的耦合性，但是当父类中的成员变量、成员函数或者类本身被 final 关键字修饰时，修饰的类不能继承，修饰的成员不能重写或修改。

多态：多态就是不同继承类的对象，对同一消息做出不同的响应，基类的指针指向或绑定到派生类的对象，使得基类指针呈现不同的表现方式。



---
# 7. 重载overload、重写（覆盖）overwrite、隐藏override的区别

1. 重载overload: 同一函数内部，函数名相同，参数不同
2. 隐藏override: 父类子类，同名，同参数的虚函数，操作覆盖（可以理解为，接口实现）      ***virtual, 可以用基类指针访问子类方法实现多态***
3. 重写（覆盖）overwrite: 父类子类，同名，参数可以不同，子类方法覆盖掉基类

Overload(重载)：在C++程序中，可以将语义、功能相似的几个函数用同一个名字表示，但参数或返回值不同（包括类型、顺序不同），即函数重载。
（1）相同的范围（在同一个类中）；
（2）函数名字相同；
（3）参数不同；
（4）virtual 关键字可有可无。

Override(覆盖)：是指派生类函数覆盖基类函数，特征是：
（1）不同的范围（分别位于派生类与基类）；
（2）函数名字相同；
（3）参数相同；
（4）基类函数必须有virtual 关键字；
（5）可以理解为，接口实现

Overwrite(重写)：是指派生类的函数屏蔽了与其同名的基类函数，规则如下：
（1）如果派生类的函数与基类的函数同名，但是参数不同。此时，不论有无virtual关键字，基类的函数将被隐藏（注意别与重载混淆）。

（2）如果派生类的函数与基类的函数同名，并且参数也相同，但是基类函数没有virtual关键字。此时，基类的函数被隐藏（注意别与覆盖混淆）。


---
# 8 虚表 多态

https://www.cnblogs.com/malecrab/p/5572730.html

虚表结构
![img](D:\code\sf\898333-20160609210402699-1501495771.png)

构造过程
![img](D:\code\sf\898333-20160609210418246-1188626035.png)

调用过程
![img](D:\code\sf\898333-20160609210434386-1391536209.png)

---
# 9 虚析构

\1.   虚析构问题只出现在，父类指针调用子类方法的时候，且子类为堆中申请的对象（动态对象），此时为动态调用。其他情况编译器在编译阶段就一直到确切的对象和对象方法，因此会在编译期间静态链接。           CBase *pb = new CDerived; 

\2.   若基类析构函数为非虚，则基类析构为静态，编译器在用**基类指针delete对象**的时候，静态链接父类析构函数。当通过**基类指针 `delete` 子类对象**时（如 `delete pb;`，其中 `pb` 是 `CBase*` 类型，指向 `new CDerived` 创建的对象），**只会调用基类的析构函数，不会调用子类的析构函数**。非虚函数的调用在编译期通过 “静态绑定” 确定 —— 编译器仅根据指针的**声明类型**（而非实际指向的对象类型）来绑定函数。此处 `pb` 的声明类型是 `CBase*`，因此 `delete pb` 会静态链接到 `CBase` 的析构函数，完全忽略 `CDerived` 的析构函数。

2.1 delete pb, 如果基类析构没有virtual，pb的析构就是编译器决定的，用CBase的析构（因为是CBase的指针），delete的时候只会调CBase的析构，不会调子类的

\3.   若基类析构函数为虚函数，则基类析构为动态，编译器会把基类析构函数放入虚表中，在对象释放的地方会去虚表找析构函数（编译器会根据virtual决定是动态链接还是静态链接），在子类构造的时候子类会把自己的析构函数在虚表里覆盖基类的析构。

\4.   析构函数是链式循环调用的，即只要调用子类析构，在子类析构中会自动调用父类析构。因此虚表中只要记录最下层子类的析构函数，并在对象释放的时候调用这个子类析构函数，就会链式的调用所有基类的析构

\5.   没有visual 声明就是静态编译地址，直接调用函数的位置，有visual声明就是动态的调用，调用的时候会查找虚表，根据虚表调用函数


---
# 10 explicit

避免Test2 t3 = 14;这种情况出现，容易出现错误。禁止隐式转换防止歧义



---
# 11 static 局部变量/成员变量
静态变量，有和全局变量一样的生命周期，也放在全局区域，但是作用域，根据位置具体看

https://blog.csdn.net/leikun153/article/details/80563903

1.  静态局部变量被static修饰，**放在全局区域，具有和全局变量一样的声明周期，但是作用域根据变量位置的不同而不同，只需初始化一次**

2.  默认初始值不同，静态局部变量默认初始值为0，但其如果人为初始化只执行一次，后面的初始化都不会再执行。而普通局部变量初始值随机，但是每一次合法初始化都会执行

3.  静态函数的作用域为当前的源文件，静态函数会被一直放在一个一直使用的存储区，直到退出应用程序实列

4.  静态数据成员是属于整个类的，而不是属于某个对象。即不管实例多少个对象，它们都公用一个静态数据成员

5.  静态数据成员(static int b )则必须在类外初始化（int 类名::b=100），这是因为静态数据成员不属于任何一个对象，而是属于整个类的

6.  静态成员函数也是属于整个类的，但是不属于任何一个对象。它是某个类所有对象共享的一个函数。普通的成员函数都有一个this指针，this指针用来指向类的对象本身。因为静态成员函数不属于任何对象，所以静态数据成员函数并没有this指针，所以它无法来访问普通的数据成员，它只能调用其余的静态成员

7.  静态成员函数可以被该类的所有对象直接访问；静态成员函数本身只能访问静态成员，不可以访问非静态成员

8.  没有this指针，因此只能访问静态成员

9.  静态成员变量是在类内进行声明，在类外进行定义和初始化，在类外进行定义和初始化的时候不要出现 static 关键字和private、public、protected 访问规则

10.  静态成员变量可以作为成员函数的参数，而普通成员变量不可以

     ```
     #include <iostream>
     using namespace std;
     
     class A
     {
     public:
         static int s_var;
         int var;
         void fun1(int i = s_var); // 正确，静态成员变量可以作为成员函数的参数
         void fun2(int i = var);   //  error: invalid use of non-static data member 'A::var'
     };
     int main()
     {
         return 0;
     }
     
     ```

11.  静态数据成员的类型可以是所属类的类型，而普通数据成员的类型只能是该类类型的指针或引用。

```
#include <iostream>
using namespace std;

class A
{
public:
    static A s_var; // 正确，静态数据成员
    A var;          // error: field 'var' has incomplete type 'A'
    A *p;           // 正确，指针
    A &var1;        // 正确，引用
};

int main()
{
    return 0;
}
```

12.    **静态成员函数不能声明成虚函数（`virtual`）、`const` 函数和 `volatile` 函数**

13.    为何static成员函数不能为const函数

-   当声明一个非静态成员函数为const时，对this指针会有影响。对于一个Test类中的const修饰的成员函数，this指针相当于Test const *, 而对于非const成员函数，this指针相当于Test *.

-   而static成员函数没有this指针，所以使用const来修饰static成员函数没有任何意义。
-   volatile的道理也是如此。

14.    为何static成员函数不能为virtual

a.    static成员不属于任何类对象或类实例，所以即使给此函数加上virutal也是没有任何意义的。
b.    **静态与非静态成员函数之间有一个主要的区别。那就是静态成员函数没有this指针。**

-   虚函数依靠vptr和vtable来处理。vptr是一个指针，在类的构造函数中创建生成，并且只能用this指针来访问它，因为它是类的一个成员，并且vptr指向保存虚函数地址的vtable.
-   对于静态成员函数，它没有this指针，所以无法访问vptr. 这就是为何static函数不能为virtual.
-   虚函数的调用关系：this -> vptr -> vtable ->virtual function


# 12 数据存储layout
-   **全局区/静态存储区（.bss 段和 .data 段）**：存放全局变量和静态变量，程序运行结束操作系统自动释放，在 C 语言中，未初始化的放在 .bss 段中，初始化的放在 .data 段中，C++ 中不再区分了。
-   **常量存储区（.rodata 段）**：存放的是常量，不允许修改，程序运行结束自动释放。
-   **代码区（.text 段）**：存放代码，不允许修改，但可以执行。编译后的二进制文件存放在这里。



# 13 类成员const
-   const 成员变量：

const 成员变量只能在类内声明、定义，**在构造函数初始化列表中初始化**。
const 成员变量只在某个对象的生存周期内是常量，对于整个类而言却是可变的，因为类可以创建多个对象，不同类的 const 成员变量的值是不同的。因此不能在类的声明中初始化 const 成员变量，类的对象还没有创建，编译器不知道他的值。

-   const 成员函数：

不能修改成员变量的值，除非有 mutable 修饰；只能访问成员变量。
不能调用非常量成员函数，以防修改成员变量的值。

`const` 相比define的优点：

-   有数据类型，在定义式可进行安全性检查。
-   可调式。
-   占用较少的空间。


# 14 inline
1. **inline 关键字应该在函数定义中，不是在声明**
2. **inline函数应该都放在头文件中**，连同函数的定义，单独的函数声明不起作用
3. 类中声明时的函数定义默认为inline
4. 在cpp文件中的inline函数定义，只在这个cpp文件中有效，其他文件找不到这个定义
5. inline其实是连同定义绑定在一起的一种声明。可以往#define上理解

缺点，代码膨胀

1.  只能小于10行的函数inline
2.  函数有复杂逻辑不适合

https://www.cnblogs.com/jian-99/p/9049803.html

However, this is only a "**hint**", and the compiler may ignore it, and most compilers will try to "inline" even when the keyword is not used, as part of the optimizations, where its possible.

there's no difference between `static` and `static inline` function definitions

# 15 static inline

这是两个关键字，static负责作用域，inline负责内联。static inline放在头文件的函数定义上，static实际作用在应用这个头文件的cpp文件里面

# 16 类对象构造顺序
We will now create a DeriDemo obj ect.
This is the BaseDemo constructor.
This is the DeriDemo constructor.
The program is now going to,end.
This is the DeriDemo destructor.
This is the BaseDemo destructor.

# 17 new 和 malloc 的区别，delete 和 free 的区别
在使用的时候 new、delete 搭配使用，malloc、free 搭配使用。
-   malloc、free 是库函数，而new、delete 是关键字。
-   new 申请空间时，无需指定分配空间的大小，编译器会根据类型自行计算；malloc 在申请空间时，需要确定所申请空间的大小。
-   new 申请空间时，返回的类型是对象的指针类型，无需强制类型转换，是类型安全的操作符；malloc 申请空间时，返回的是 void* 类型，需要进行强制类型的转换，转换为对象类型的指针。
-   new 分配失败时，会抛出 bad_alloc 异常，malloc 分配失败时返回空指针。
-   对于自定义的类型，new 首先调用 operator new() 函数申请空间（底层通过 malloc 实现），然后调用构造函数进行初始化，最后返回自定义类型的指针；delete 首先调用析构函数，然后调用 operator delete() 释放空间（底层通过 free 实现）。malloc、free 无法进行自定义类型的对象的构造和析构。
-   new 操作符从自由存储区上为对象动态分配内存，而 malloc 函数从堆上动态分配内存。（自由存储区不等于堆）

**malloc 的原理:**

-   当开辟的空间小于 128K 时，调用 brk() 函数，通过移动 _enddata 来实现；
-   当开辟空间大于 128K 时，调用 mmap() 函数，通过在虚拟地址空间中开辟一块内存空间来实现。



# 18 union
-   联合体和结构体都是由若干个数据类型不同的数据成员组成。使用时，**联合体只有一个有效的成员**, 而结构体所有的成员都有效
-   联合体的大小为其内部所有变量的最大值，按照最大类型的倍数进行分配大小；结构体分配内存的大小遵循内存对齐原则。

# 19 volatile
- volatile 对编译器的影响：使用该关键字后，编译器不会对相应的对象进行优化，即不会将变量从内存缓存到寄存器中，防止多个线程有可能使用内存中的变量，有可能使用寄存器中的变量，从而导致程序错误。
- 问题：多线程（或硬件交互）下的 “缓存不一致”
  如果变量 `x` 可能被**其他线程**或**硬件设备**（如传感器、外设）修改，这种优化就会出问题：

  - 线程 A 中，编译器将 `x` 缓存到寄存器，后续读取的是寄存器中的 “旧值”；
  - 同时，线程 B 或硬件修改了内存中 `x` 的值，导致**寄存器中的值与内存中的值不一致**；
  - 线程 A 因读取到旧值，执行错误逻辑。

- `volatile` 的作用：禁止缓存优化，强制访问内存

  `volatile` 关键字的含义是 “**易变的**”，告诉编译器：
-   volatile 关键字和 const 关键字可以同时使用，某种类型可以既是 volatile 又是 const ，同时具有二者的属性。
# 20 虚函数表
-   **虚函数表和类绑定，虚表指针和对象绑定**。
-   当子类没有虚函数，但是父类存在虚函数的时候，子类依旧会新建一个虚表，虚表里面存放的是父类的虚函数指针。
-   当子类没有虚函数时，子类对象的虚指针指向基类的虚函数表，子类没有虚函数表。但是当子类重写基类的虚函数时或者添加了一个新的虚函数时，子类自己会拥有一个属于自己的虚函数表，表中会存放基类虚函数的地址。

# 21 RTTI
RTTI(Run Time Type Identification)即通过运行时类型识别，程序能够使用基类的指针或引用来检查着这些指针或引用所指的对象的实际派生类型。
RTTI提供了两个非常有用的操作符：typeid和dynamic_cast。

- typeid操作符，返回指针和引用所指的实际类型；

  - #### **`typeid` 的关键特性**

    1. **作用于对象**：`typeid(对象)` 才能获取实际类型（如 `typeid(*b2)`）；若作用于指针（`typeid(b2)`），获取的是指针本身的类型（`Base*`）。
    2. **依赖虚函数**：若基类**没有虚函数**，`typeid` 会根据指针的**静态类型**（编译期类型）判断，而非对象的实际类型（动态类型）。
    3. **主要用途**：类型比较（`typeid(a) == typeid(b)`）或获取类型名称。

- dynamic_cast操作符，将基类类型的指针或引用安全地转换为其派生类类型的指针或引用。

  - #### **`dynamic_cast` 的关键特性**

    1. **依赖虚函数**：基类必须包含至少一个虚函数，否则无法使用 `dynamic_cast`（编译报错）。
    2. **安全检查**：转换时会在**运行时检查**对象的实际类型：
       - 若基类指针 / 引用确实指向派生类对象，转换成功，返回有效指针 / 引用；
       - 若指向的是基类对象或其他类型，指针转换返回 `nullptr`，引用转换抛出 `std::bad_cast` 异常。
    3. **主要用途**：向下转型（基类 → 派生类），以便访问派生类的成员。


# 22 多继承覆盖

![image.png](D:\code\sf\1612679879-DBSJce-image.png)

![image.png](D:\code\sf\1612682139-oQZazN-image.png)

# 23 继承关系

![img](D:\code\sf\1203656-20180914195333561-1755431897.png)

![img](D:\code\sf\1203656-20180914195431843-593420743.png)

![img](D:\code\sf\1203656-20180914195458205-1306227218.png)

# 24 为什么初始化列表快

少调用一些构造

![在这里插入图片描述](D:\code\sf\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow.png)

# 25 std::list::splice
std::list 中与元素移动相关的方法有如下几种：s

splice(const_iterator position, list& other): 将 other 中的所有元素移动到 list 的指定位置。
splice(const_iterator position, list&& other): 将 other 中的所有元素移动到 list 的指定位置（移动语义）。
splice(const_iterator position, other, const_iterator i): 将 other 中从 i 开始的单个元素移动到 list 的指定位置。
splice(const_iterator position, other, const_iterator first, const_iterator last): 将 other 中从 first 到 last 的元素移动到 list 的指                
原文链接：https://blog.csdn.net/h8062651/article/details/136417035



# 三种继承方式的具体区别
以下通过表格清晰对比三种继承方式对基类成员权限的影响，以及派生类外部的访问规则：

| 基类成员原有权限  | public继承（公有继承）                                       | protected继承（保护继承）                                    | private继承（私有继承）                                      |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **public成员**    | 派生类中为`public`：<br>1. 派生类内部可访问<br>2. 派生类外部可访问（如`派生类对象.基类public成员`） | 派生类中为`protected`：<br>1. 派生类内部可访问<br>2. 派生类外部不可访问 | 派生类中为`private`：<br>1. 派生类内部可访问<br>2. 派生类外部不可访问 |
| **protected成员** | 派生类中为`protected`：<br>1. 派生类内部可访问<br>2. 派生类外部不可访问 | 派生类中为`protected`：<br>1. 派生类内部可访问<br>2. 派生类外部不可访问 | 派生类中为`private`：<br>1. 派生类内部可访问<br>2. 派生类外部不可访问 |
| **private成员**   | 派生类中不可直接访问（需通过基类public/protected函数间接访问） | 派生类中不可直接访问（同左）                                 | 派生类中不可直接访问（同左）                                 |
| **核心特点**      | “接口继承”：派生类完整继承基类的`public`接口（外部可通过派生类对象使用基类public功能），是最常用的继承方式（如“学生类继承人类”） | “部分接口隐藏”：基类`public`成员在派生类中变为`protected`，外部无法直接访问，仅允许派生类内部或“派生类的子类”使用（如“内部工具类继承基础工具类，不对外暴露全部接口”） | “完全接口隐藏”：基类`public/protected`成员在派生类中均变为`private`，仅允许当前派生类内部使用，**派生类的子类无法访问基类成员**（如“临时功能类继承基础类，仅自身使用基类功能，不允许后续继承扩展”） |







# C/C++中`const char* a`  是a的内容是const吗

### 核心结论

`const char* a` 中，**不是变量 `a` 指向的内容（内存）是 const，而非 `a` 本身是 const**—— 更通俗地说：`a` 这个指针变量可以指向不同的内存地址，但通过 `a` 无法修改它所指向的内存里的字符内容。

### 拆解语法含义（从右往左读更易理解）

C/C++ 中指针的 `const` 修饰遵循「**修饰谁，谁就不可变**」的规则，拆解 `const char* a`：

| 部分         | 含义                                                     |
| ------------ | -------------------------------------------------------- |
| `char`       | 指针指向的内存存储的是字符类型                           |
| `const char` | 被指向的字符是「只读」的（const 修饰 `char`，而非 `*a`） |
| `*`          | `a` 是一个指针，指向 `const char` 类型的内存             |
| `a`          | 指针变量本身是可变的（可重新赋值指向其他地址）           |

### 对比示例：清晰区分「内容不可改」和「指针不可改」

通过代码示例直观理解差异：

#### 1. `const char* a`（内容不可改，指针可改）

```cpp
#include <stdio.h>
int main() {
    char str[] = "hello";
    const char* a = str; // a 指向 str 的首地址
    
    // 错误：通过 a 修改指向的内容（const 修饰的是 char，内容只读）
    // *a = 'H'; 
    
    // 正确：a 本身是普通指针，可重新指向其他地址
    char str2[] = "world";
    a = str2; 
    printf("%s\n", a); // 输出 world
    return 0;
}
```

#### 2. 反向对比：`char* const a`（指针不可改，内容可改）

如果想让「指针本身不可改，内容可改」，需把 `const` 放在 `*` 右侧：

```cpp
#include <stdio.h>
int main() {
    char str[] = "hello";
    char* const a = str; // a 是 const 指针，指向不可变
    
    // 正确：内容可改（const 没修饰 char）
    *a = 'H'; 
    printf("%s\n", a); // 输出 Hello
    
    // 错误：指针本身不可改，不能重新指向其他地址
    // char str2[] = "world";
    // a = str2; 
    return 0;
}
```

#### 3. 终极场景：`const char* const a`（内容和指针都不可改）

```cpp
char str[] = "hello";
const char* const a = str;
// *a = 'H'; // 错误：内容不可改
// a = str2; // 错误：指针不可改
```

### 常见误区纠正

1. 误区：「`const char* a` 是 `a` 不可改」→ 纠正：`a` 是普通指针，可改指向；不可改的是 `*a`（指向的内容）。
2. 误区：「`const char*` 和 `char const*` 不一样」→ 纠正：二者完全等价（`const` 修饰 `char`），只是书写习惯不同。
3. 误区：「指向常量的指针，指向的内存一定是只读内存」→ 纠正：`a` 可指向普通可写内存（如示例中的 `str`），只是通过 `a` 无法修改；直接操作 `str` 仍可改（如 `str[0] = 'H'` 是合法的）。

### 总结口诀

- `const 在前（const char*）`：内容不可改，指针可改；

- `const 在后（char* const）`：指针不可改，内容可改；

- `前后都有（const char* const）`：内容和指针都不可改。

    

# C++中const string& a是不是也是只能a的内容不能改，另外这个在做函数参数的时候为什么性能更高

### 一、`const string& a` 的核心含义：引用不可改 + 内容不可改

`const string& a` 是「常量引用」，和 `const char* a` 的逻辑有相似性，但因「引用 vs 指针」的本质差异，规则更简单：

| 部分      | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| `string&` | `a` 是 `string` 对象的**引用**（别名），引用本身不可被重新绑定（引用一旦初始化，永远指向同一个对象，这是引用的特性，和 `const` 无关）； |
| `const`   | 修饰 `string`，表示**通过这个引用 `a` 无法修改所指向的 `string` 对象的内容**； |

#### 关键示例验证

```cpp
#include <string>
using namespace std;

int main() {
    string str = "hello";
    const string& a = str;

    // 错误：通过 const 引用修改内容（被 const 禁止）
    // a[0] = 'H'; 
    // a.append(" world");

    // 注意：直接修改原对象 str 是允许的（const 仅限制引用 a，不限制原对象）
    str[0] = 'H'; 
    // a 会同步看到修改（因为引用是原对象的别名），输出 "Hello"
    cout << a << endl; 

    // 错误：引用本身不可重新绑定（引用的特性，和 const 无关）
    // string str2 = "world";
    // a = str2; 
    return 0;
}
```

#### 与 `const char* a` 的核心区别

- `const char* a`：指针可改指向（`a = str2` 合法），但内容不可改；
- `const string& a`：引用不可改绑定（天生特性），且内容不可改；

### 二、`const string&` 作为函数参数性能更高的核心原因

函数参数用 `const string&` 而非直接传 `string`（值传递），核心是**避免「拷贝构造」和「析构」的开销**，尤其当 `string` 存储大量字符时，性能差异会极其显著。

#### 1. 先明确：值传递 vs 常量引用传递的底层差异

| 传递方式                              | 底层行为                                                     | 性能开销                                                     |
| ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `void f(string s)`（值传递）          | 调用函数时，会创建实参的**拷贝**（调用 `string` 的拷贝构造函数）；函数结束时，拷贝的临时对象会被析构（调用析构函数）。 | 拷贝开销：`string` 的拷贝是「深拷贝」（需要复制底层字符数组），字符越多，拷贝越慢；析构开销：释放临时对象的内存，额外消耗 CPU。 |
| `void f(const string& s)`（常量引用） | 仅传递实参的「内存地址」（引用本质是指针的语法糖），没有拷贝、没有临时对象。 | 几乎无开销：仅传递一个地址（8 字节，64 位系统），无需拷贝字符数组，也无析构。 |

#### 2. 性能差异的直观示例

假设传递一个存储 100 万字符的 `string`：

- 值传递：需要复制 100 万字节（约 1MB）的内存，耗时约微秒级到毫秒级（取决于硬件）；
- 常量引用：仅传递 8 字节的地址，耗时纳秒级，几乎可以忽略。

#### 3. 为什么加 `const`？—— 安全 + 兼容临时对象

- **安全性**：避免函数内部误修改实参（比如函数里写 `s.append("xxx")`），保证实参的只读性；

- **兼容性**：`const` 引用可以绑定临时对象（比如 `f("hello")` 或 `f(str1 + str2)`），而非 `const` 引用不行。例如：

    ```cpp
    // 合法：const 引用可绑定临时对象（"hello" 会隐式生成临时 string）
    f(const string& s) { ... }
    f("hello"); 
    
    // 非法：非 const 引用不能绑定临时对象
    f(string& s) { ... }
    f("hello"); // 编译报错
    ```

    

#### 4. 补充：什么时候值传递反而更好？

如果函数内部需要频繁修改参数（且不想影响实参），且 `string` 长度极短（比如几个字符），值传递的开销可能比引用更小（因为引用有指针解引用的微小开销）。但这种场景极少，**大部分场景下 `const string&` 是最优选择**。

### 三、核心总结

1. `const string& a`：
    - 引用本身不可重新绑定（引用的天生特性）；
    - 通过 `a` 无法修改所指向的 `string` 对象内容（`const` 的作用）；
    - 原对象若为非 `const`，直接修改原对象仍会影响 `a`（因为引用是别名）。
2. 函数参数用 `const string&` 性能高的原因：
    - 避免 `string` 的深拷贝（核心）和析构开销；
    - `const` 保证只读安全，同时兼容临时对象，是语法和逻辑上的双重保障。

C++ unique_ptr是怎么用的需要注意什么

### C++ `unique_ptr` 完全指南：用法与注意事项

`unique_ptr` 是 C++11 引入的**独占式智能指针**，用于管理动态分配的资源，确保资源自动释放（即使发生异常），核心特点是**所有权唯一**（同一时间只有一个 `unique_ptr` 指向某资源），避免内存泄漏和手动管理 `delete` 的繁琐。

------

## 一、基本用法

### 1. 头文件

必须包含 `<memory>` 头文件：

cpp



运行









```cpp
#include <memory>
```

### 2. 创建 `unique_ptr`

#### （1）空指针

cpp



运行









```cpp
std::unique_ptr<int> p1; // 空 unique_ptr，不指向任何资源
if (!p1) { // 判空
    std::cout << "p1 is null\n";
}
```

#### （2）指向动态分配的对象

- **C++11 推荐方式**（`std::make_unique`，C++14 引入，更安全）：

    cpp

    

    运行

    

    

    

    

    ```cpp
    // 指向 int 类型，值为 42
    auto p2 = std::make_unique<int>(42);
    // 指向自定义类型（如 std::string）
    auto p3 = std::make_unique<std::string>("hello");
    // 指向数组（C++14 起支持）
    auto p4 = std::make_unique<int[]>(5); // 分配 5 个 int 的数组，值默认初始化
    ```

    

- **手动构造（不推荐，易出错）**：

    cpp

    

    运行

    

    

    

    

    ```cpp
    // 直接用 new 构造（需手动匹配 delete/delete[]）
    std::unique_ptr<int> p5(new int(10));
    std::unique_ptr<int[]> p6(new int[5]{1,2,3,4,5}); // 数组版本
    ```

    

### 3. 访问资源

通过 `*`（解引用）或 `->`（成员访问），与普通指针一致：

cpp



运行









```cpp
struct MyClass {
    void print() { std::cout << "MyClass\n"; }
    int val = 0;
};

auto ptr = std::make_unique<MyClass>();
ptr->print();       // 成员访问：输出 MyClass
std::cout << (*ptr).val << "\n"; // 解引用：输出 0
std::cout << ptr.get() << "\n";  // get()：获取原始指针（谨慎使用）
```

### 4. 释放 / 重置资源

- `reset()`：释放当前资源，指向新资源（或空）：

    cpp

    

    运行

    

    

    

    

    ```cpp
    auto ptr = std::make_unique<int>(42);
    ptr.reset(); // 释放 int(42)，ptr 变为空
    ptr.reset(new int(100)); // 释放旧资源（若有），指向新 int(100)
    ```

    

- `release()`：放弃所有权，返回原始指针（**不会释放资源**，需手动管理）：

    cpp

    

    运行

    

    

    

    

    ```cpp
    auto ptr = std::make_unique<int>(42);
    int* raw_ptr = ptr.release(); // ptr 变为空，raw_ptr 指向 42
    delete raw_ptr; // 必须手动 delete，否则内存泄漏
    ```

    

### 5. 所有权转移

`unique_ptr` 不支持拷贝（`copy`），但支持**移动（move）**（通过 `std::move` 转移所有权）：

cpp



运行









```cpp
auto p1 = std::make_unique<int>(42);
// std::unique_ptr<int> p2 = p1; // 编译错误：禁止拷贝
std::unique_ptr<int> p2 = std::move(p1); // 合法：所有权转移给 p2
// 此时 p1 为空，p2 指向 42
if (!p1) {
    std::cout << "p1 is null after move\n";
}
```

### 6. 作为函数参数 / 返回值

- **参数**：推荐传值（触发移动）或传引用（避免转移所有权）：

    cpp

    

    运行

    

    

    

    

    ```cpp
    // 传值：所有权转移给函数
    void func1(std::unique_ptr<int> ptr) {
        std::cout << *ptr << "\n";
    }
    // 传引用：仅访问，不转移所有权
    void func2(const std::unique_ptr<int>& ptr) {
        if (ptr) std::cout << *ptr << "\n";
    }
    
    // 调用示例
    auto ptr = std::make_unique<int>(42);
    func1(std::move(ptr)); // 必须 move，ptr 变为空
    func2(ptr); // 直接传引用，ptr 仍有效
    ```

    

- **返回值**：直接返回 `unique_ptr`（编译器会优化为移动，无需手动 `std::move`）：

    ```cpp
    std::unique_ptr<int> create_int(int val) {
        return std::make_unique<int>(val); // 自动移动
    }
    
    auto ptr = create_int(42); // 合法，所有权转移给 ptr
    ```

    

### 7. 自定义删除器

默认删除器是 `delete`/`delete[]`，可自定义删除器（如管理文件句柄、动态库句柄）：

```cpp
// 示例1：管理 FILE*（自定义删除器关闭文件）
auto file_deleter = [](FILE* fp) {
    if (fp) fclose(fp);
    std::cout << "File closed\n";
};
std::unique_ptr<FILE, decltype(file_deleter)> fp(fopen("test.txt", "w"), file_deleter);

// 示例2：管理数组（显式指定 delete[]，C++11 需手动，C++14 可省略）
std::unique_ptr<int[], std::default_delete<int[]>> arr(new int[5]);
```

------

## 二、核心注意事项

### 1. 禁止拷贝，仅支持移动

`unique_ptr` 的拷贝构造 / 赋值运算符被**删除**，必须通过 `std::move` 转移所有权：

```cpp
std::unique_ptr<int> p1 = std::make_unique<int>(42);
std::unique_ptr<int> p2 = p1; // 编译错误！
std::unique_ptr<int> p3 = std::move(p1); // 正确
```

### 2. 避免裸指针与 `unique_ptr` 混用

- 不要让裸指针和 `unique_ptr` 同时指向同一资源（否则可能重复释放）：

    ```cpp
    int* raw = new int(42);
    std::unique_ptr<int> ptr(raw);
    delete raw; // 错误：ptr 析构时会再次 delete，双重释放！
    ```

    

- 避免用 `get()` 返回的裸指针创建新的 `unique_ptr`：

    ```cpp
    auto ptr = std::make_unique<int>(42);
    std::unique_ptr<int> ptr2(ptr.get()); // 错误：ptr 和 ptr2 共享资源，析构时双重释放
    ```

    

### 3. 数组处理注意事项

- C++11 中 `unique_ptr<T[]>` 不支持 `*`/`->`，需用 `[]` 访问元素：

    ```cpp
    auto arr = std::make_unique<int[]>(5);
    arr[0] = 10; // 正确
    // *arr = 10; // 编译错误：数组版本不支持解引用
    ```

    

- 不要用 `unique_ptr<T>` 管理数组（默认删除器是 `delete`，而非 `delete[]`，会导致未定义行为）：

    ```cpp
    std::unique_ptr<int> bad_arr(new int[5]); // 错误！析构时调用 delete，而非 delete[]
    std::unique_ptr<int[]> good_arr(new int[5]); // 正确
    ```

    

### 4. 不要将 `unique_ptr` 存储到不支持移动的容器

- 早期 C++ 容器（如 `std::vector`）若存储 `unique_ptr`，需确保容器操作支持移动（C++11 及以上已支持）：

    ```cpp
    std::vector<std::unique_ptr<int>> vec;
    vec.push_back(std::make_unique<int>(1)); // 正确（C++11 起，push_back 支持移动）
    vec.emplace_back(2); // 更高效：直接构造，无需移动
    // vec[0] = std::make_unique<int>(3); // 正确（移动赋值）
    ```

    

### 5. 警惕悬空指针

- `release()` 仅放弃所有权，不释放资源，必须手动处理返回的裸指针：

    ```cpp
    auto ptr = std::make_unique<int>(42);
    int* raw = ptr.release();
    // 忘记 delete raw → 内存泄漏！
    delete raw; // 必须手动释放
    ```

    

### 6. 自定义删除器的类型匹配

- 自定义删除器会改变 `unique_ptr` 的类型，需确保类型一致：

    ```cpp
    auto deleter = [](int* p) { delete p; };
    std::unique_ptr<int, decltype(deleter)> p1(new int(42), deleter);
    // std::unique_ptr<int> p2 = p1; // 编译错误：类型不匹配（删除器不同）
    ```

    

### 7. 不要用 `unique_ptr` 管理栈对象

`unique_ptr` 仅用于管理**动态分配**（`new`/`new[]`）的资源，管理栈对象会导致析构时调用 `delete`，触发未定义行为：

```cpp
int val = 42;
std::unique_ptr<int> ptr(&val); // 错误！析构时 delete &val → 栈内存被释放
```

### 8. 异常安全

- `make_unique` 比手动 `new` 更安全：若 `new` 后抛出异常，`make_unique` 会确保资源被释放，而手动 `new` 可能泄漏：

    ```cpp
    // 不安全：若 func() 抛出异常，new int(42) 的资源未被释放
    std::unique_ptr<int> ptr(new int(42));
    func(); 
    
    // 安全：make_unique 原子化分配+构造，异常时自动释放
    auto ptr = std::make_unique<int>(42);
    func();
    ```

    

------

## 三、总结

| 特性 / 操作 | 正确用法                          | 错误示例                                   |
| ----------- | --------------------------------- | ------------------------------------------ |
| 创建        | `std::make_unique<T>(...)`        | `std::unique_ptr<T>(new T(...))`（不推荐） |
| 所有权转移  | `std::move(ptr)`                  | 直接拷贝 `ptr`                             |
| 数组管理    | `std::unique_ptr<T[]>`            | `std::unique_ptr<T>(new T[N])`             |
| 裸指针      | 仅 `get()` 临时访问，不持有所有权 | 用 `get()` 创建新 `unique_ptr`             |
| 删除器      | 类型匹配，资源正确释放            | 忘记自定义删除器释放资源                   |

`unique_ptr` 是 C++ 中**最常用的智能指针**，核心优势是**零开销**（与裸指针性能一致）+ **独占所有权**，优先用于管理单个动态资源，替代手动 `new/delete`，大幅降低内存泄漏风险。

unique_ptr和以前的auto_ptr区别在哪，为什么auto_ptr被废弃了

### `unique_ptr` vs `auto_ptr`：核心区别与 `auto_ptr` 被废弃的原因

`auto_ptr` 是 C++98 引入的首个 “智能指针”，旨在解决动态资源自动释放问题，但设计存在**致命缺陷**；C++11 引入 `unique_ptr` 彻底替代 `auto_ptr`，并在 C++17 正式将 `auto_ptr` 标记为废弃（C++20 移除）。以下从**核心区别**和 `auto_ptr` 的废弃原因两方面详解：

------

## 一、`unique_ptr` 与 `auto_ptr` 的核心区别

| 特性 / 维度         | `auto_ptr` (C++98)                             | `unique_ptr` (C++11+)                                |
| ------------------- | ---------------------------------------------- | ---------------------------------------------------- |
| **所有权语义**      | 伪独占（拷贝时 “偷偷转移所有权”）              | 严格独占（禁止拷贝，仅支持显式移动）                 |
| **拷贝 / 赋值行为** | 允许拷贝，但拷贝后原指针变为空（隐式转移）     | 拷贝构造 / 赋值被删除，仅能通过 `std::move` 显式移动 |
| **数组支持**        | 不支持（仅能管理单个对象，管理数组会析构错误） | 原生支持 `unique_ptr<T[]>`，适配 `delete[]`          |
| **自定义删除器**    | 不支持                                         | 支持自定义删除器（类型安全，灵活）                   |
| **容器兼容性**      | 无法安全存入标准容器（拷贝时隐式失效）         | 可安全存入容器（仅移动，无隐式失效）                 |
| **异常安全**        | 拷贝时可能导致资源泄漏（隐式转移不可控）       | 移动语义明确，异常安全（`make_unique` 原子化）       |
| **返回值优化**      | 拷贝返回时可能触发不必要的所有权转移           | 编译器自动优化为移动，无需手动 `std::move`           |

### 1. 最核心：所有权转移的 “显式 vs 隐式”

这是两者最本质的区别，也是 `auto_ptr` 被废弃的核心原因：

#### （1）`auto_ptr`：隐式、危险的所有权转移

`auto_ptr` 允许**拷贝构造 / 赋值**，但拷贝时会**隐式转移所有权**（原指针被置空），这种 “静默失效” 极易导致逻辑错误和崩溃：

```cpp
#include <memory>
#include <iostream>
using namespace std;

int main() {
    auto_ptr<int> ap1(new int(42));
    auto_ptr<int> ap2 = ap1; // 看似普通拷贝，实则隐式转移所有权
    
    cout << (ap1.get() == nullptr) << endl; // 输出 1（ap1 已空）
    // *ap1 = 100; // 运行时崩溃！ap1 是空指针
    return 0;
}
```

**问题**：开发者很容易误以为 `ap1` 仍有效，后续访问导致悬空指针，这种 “隐式行为” 违反了 C++ “明确性” 原则。

#### （2）`unique_ptr`：显式、可控的所有权转移

`unique_ptr` 直接**删除了拷贝构造 / 赋值运算符**，强制通过 `std::move` 显式转移所有权，从语法上杜绝了隐式失效：

```cpp
#include <memory>
#include <iostream>
using namespace std;

int main() {
    unique_ptr<int> up1 = make_unique<int>(42);
    // unique_ptr<int> up2 = up1; // 编译错误！禁止拷贝
    unique_ptr<int> up2 = move(up1); // 显式 move，所有权转移
    
    cout << (up1.get() == nullptr) << endl; // 输出 1（up1 空）
    // *up1 = 100; // 编译期就能发现问题（若未判空）
    return 0;
}
```

**优势**：`move` 明确告知开发者 “所有权已转移”，代码意图清晰，错误暴露在编译期而非运行期。

### 2. 数组支持：`auto_ptr` 完全不兼容

`auto_ptr` 仅设计用于管理单个对象（默认删除器是 `delete`），若强行管理数组，析构时会调用 `delete` 而非 `delete[]`，导致未定义行为：

```cpp
auto_ptr<int> ap(new int[5]); // 错误！析构时调用 delete，而非 delete[]
```

`unique_ptr` 专门提供 `unique_ptr<T[]>` 版本，原生适配数组，删除器自动使用 `delete[]`：

```cpp
unique_ptr<int[]> up(new int[5]{1,2,3,4,5}); // 正确，析构调用 delete[]
up[0] = 10; // 支持下标访问
```

### 3. 容器兼容性：`auto_ptr` 无法安全存入容器

标准容器（如 `vector`）的元素要求 “可拷贝”，但 `auto_ptr` 拷贝时会隐式转移所有权，导致容器内的指针全部失效：

```cpp
vector<auto_ptr<int>> vec;
vec.push_back(auto_ptr<int>(new int(1)));
vec.push_back(auto_ptr<int>(new int(2)));
// 错误：push_back 时的拷贝会导致前一个元素的 auto_ptr 被置空
cout << *vec[0]; // 运行时崩溃！vec[0] 已空
```

`unique_ptr` 虽不可拷贝，但支持**移动语义**，C++11 后容器已适配移动操作，可安全存入：

```cpp
vector<unique_ptr<int>> vec;
vec.emplace_back(make_unique<int>(1)); // 直接构造，无拷贝
vec.push_back(move(unique_ptr<int>(new int(2)))); // 显式 move
cout << *vec[0]; // 正确，输出 1
```

### 4. 自定义删除器：`auto_ptr` 无此能力

`auto_ptr` 仅支持默认的 `delete`，无法管理非内存资源（如文件句柄、网络连接）；`unique_ptr` 支持自定义删除器，且类型安全：

```cpp
// unique_ptr 管理 FILE*，自定义删除器关闭文件
auto deleter = [](FILE* fp) { if (fp) fclose(fp); };
unique_ptr<FILE, decltype(deleter)> fp(fopen("test.txt", "w"), deleter);
```

------

## 二、`auto_ptr` 被废弃的根本原因

1. **违背 “最小惊讶原则”**：拷贝本是 “复制数据”，但 `auto_ptr` 拷贝却 “转移所有权 + 置空原指针”，开发者极易误用（如上述容器场景），导致隐蔽的运行时错误。
2. **语法层面的缺陷**：`auto_ptr` 的拷贝行为破坏了 C++ 容器的 “值语义”（容器元素拷贝后应独立有效），无法安全融入标准库。
3. **功能缺失**：不支持数组、自定义删除器，无法满足复杂场景的资源管理需求。
4. **移动语义的普及**：C++11 引入 `std::move` 和移动语义后，`unique_ptr` 可通过 “显式移动” 实现严格独占，既保留了 `auto_ptr` 的资源自动释放能力，又避免了其隐式失效的缺陷。

------

## 三、总结

| 结论                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| `auto_ptr` 的问题   | 隐式所有权转移、无数组支持、容器不兼容、功能单一，易导致隐蔽的运行时错误 |
| `unique_ptr` 的改进 | 显式移动语义、原生数组支持、容器兼容、自定义删除器，错误暴露在编译期 |
| 废弃的核心逻辑      | `auto_ptr` 的设计缺陷无法修复，`unique_ptr` 是更安全、更通用的替代方案 |

**实战建议**：无论兼容旧代码还是编写新代码，都应彻底放弃 `auto_ptr`，改用 `unique_ptr`（独占资源）/ `shared_ptr`（共享资源）。`unique_ptr` 几乎零开销（与裸指针性能一致），且安全性远超 `auto_ptr`。

unique_ptr 是线程安全的吗

### `unique_ptr` 的线程安全：核心结论与细节解析

`unique_ptr` **本身不提供线程安全保证**，其线程安全特性需分场景讨论 —— 核心原则是：`unique_ptr` 的 “独占所有权” 特性决定了它的线程安全边界，仅在**只读访问且无所有权转移**时具备有限线程安全，其余场景均需手动同步。

------

## 一、核心结论

| 场景                                                   | 是否线程安全 | 说明                                                         |
| ------------------------------------------------------ | ------------ | ------------------------------------------------------------ |
| 多线程**只读访问**同一个 `unique_ptr`（无修改 / 移动） | 是（有限）   | 仅读取 `get()`、`operator bool()` 等，无写操作，无需同步     |
| 多线程**修改 / 移动**同一个 `unique_ptr`               | 否           | 移动、`reset()`、`release()` 等写操作会导致数据竞争，触发未定义行为 |
| 多线程操作**不同 `unique_ptr` 指向的不同资源**         | 是           | 各自管理独立资源，无共享状态，天然线程安全                   |
| 多线程操作**不同 `unique_ptr` 指向的同一资源**         | 否           | 资源本身的线程安全与 `unique_ptr` 无关，需单独保护           |

------

## 二、分场景详解

### 1. 只读访问同一个 `unique_ptr`（线程安全）

若多个线程仅对同一个 `unique_ptr` 执行**只读操作**（如判空、获取原始指针、解引用读取值），且无任何线程修改它（移动、`reset` 等），则是线程安全的：

```cpp
#include <memory>
#include <thread>
#include <iostream>

std::unique_ptr<int> g_ptr = std::make_unique<int>(42);

// 只读线程：仅读取，无修改
void read_thread() {
    for (int i = 0; i < 1000; ++i) {
        if (g_ptr) { // 判空（只读）
            std::cout << *g_ptr << " "; // 解引用读取（只读）
        }
    }
}

int main() {
    std::thread t1(read_thread);
    std::thread t2(read_thread);
    t1.join();
    t2.join();
    return 0;
}
```

**原理**：只读操作不会修改 `unique_ptr` 的内部状态（如指向的指针、删除器），无数据竞争。

### 2. 写操作 / 所有权转移（线程不安全）

若多个线程对同一个 `unique_ptr` 执行**写操作**（移动、`reset()`、`release()`、赋值），或 “一读一写”，则会触发**数据竞争**（未定义行为）：

```cpp
#include <memory>
#include <thread>

std::unique_ptr<int> g_ptr = std::make_unique<int>(42);

// 写线程：修改 unique_ptr
void write_thread() {
    g_ptr.reset(new int(100)); // 重置（写操作）
}

// 读线程：同时读取
void read_thread() {
    if (g_ptr) {
        std::cout << *g_ptr << "\n"; // 可能读到无效值，甚至崩溃
    }
}

int main() {
    std::thread t1(write_thread);
    std::thread t2(read_thread);
    t1.join();
    t2.join();
    return 0;
}
```

**风险**：`unique_ptr` 的内部指针是一个普通成员变量，写操作（如 `reset`）会修改该指针，而读操作（如 `get()`）可能在修改过程中读取到 “半更新” 的无效地址，导致崩溃或数据错误。

### 3. 不同 `unique_ptr` 管理独立资源（线程安全）

多个线程操作**不同的 `unique_ptr` 实例**（即使指向同类型资源），且各自管理独立的动态资源，天然线程安全 —— 因为 `unique_ptr` 无共享状态：

```cpp
void worker(int id) {
    auto ptr = std::make_unique<int>(id); // 每个线程独立的 unique_ptr
    *ptr *= 2;
    std::cout << "Thread " << id << ": " << *ptr << "\n";
}

int main() {
    std::thread t1(worker, 1);
    std::thread t2(worker, 2);
    t1.join();
    t2.join();
    return 0;
}
```

**原理**：每个 `unique_ptr` 是线程局部的，无共享数据，无需同步。

### 4. 资源本身的线程安全（与 `unique_ptr` 无关）

`unique_ptr` 仅管理资源的所有权，**不保证其指向的资源本身是线程安全的**。若多个线程通过不同 `unique_ptr`（或裸指针）访问同一资源，需单独保护资源：

```cpp
#include <memory>
#include <thread>
#include <mutex>

std::mutex mtx;
int* g_raw = new int(42); // 共享资源
std::unique_ptr<int> g_ptr1(g_raw);
std::unique_ptr<int> g_ptr2; // 错误示例：故意让两个 unique_ptr 指向同一资源（仅演示）

void modify_resource() {
    std::lock_guard<std::mutex> lock(mtx); // 必须加锁保护资源
    *g_raw += 1;
}

int main() {
    std::thread t1(modify_resource);
    std::thread t2(modify_resource);
    t1.join();
    t2.join();
    return 0;
}
```

⚠️ 注意：上述代码中让两个 `unique_ptr` 指向同一资源是**错误的**（会导致双重释放），仅为说明 “资源本身需线程安全保护”；正确做法是让唯一的 `unique_ptr` 管理资源，多线程通过该 `unique_ptr` 访问资源时加锁。

------

## 三、如何让 `unique_ptr` 支持线程安全的写操作？

若需多线程修改同一个 `unique_ptr`（如动态替换资源），需通过**互斥锁（`std::mutex`）** 或**原子操作**保护：

```cpp
#include <memory>
#include <thread>
#include <mutex>

std::unique_ptr<int> g_ptr = std::make_unique<int>(42);
std::mutex g_mtx;

// 线程安全的重置操作
void safe_reset(int val) {
    std::lock_guard<std::mutex> lock(g_mtx); // 加锁独占访问
    g_ptr.reset(new int(val));
}

// 线程安全的读取操作
int safe_read() {
    std::lock_guard<std::mutex> lock(g_mtx);
    return g_ptr ? *g_ptr : -1;
}

int main() {
    std::thread t1(safe_reset, 100);
    std::thread t2(safe_reset, 200);
    t1.join();
    t2.join();
    std::cout << safe_read() << "\n"; // 输出 200（最后一次修改）
    return 0;
}
```

**关键**：所有对 `unique_ptr` 的写操作（及读写混合操作）必须在同一把锁的保护下执行，确保独占访问。

------

## 四、常见误区澄清

1. **误区 1**：“智能指针一定是线程安全的”

    

    ❌ 纠正：所有 C++ 标准智能指针（

    ```
    unique_ptr
    ```

    /

    ```
    shared_ptr
    ```

    /

    ```
    weak_ptr
    ```

    ）均

    不默认提供线程安全

    ，需手动同步。

2. **误区 2**：“`shared_ptr` 比 `unique_ptr` 更线程安全”

    

    ❌ 纠正：

    ```
    shared_ptr
    ```

     

    仅对 “引用计数的修改” 是原子的（多线程增减引用计数安全），但修改

     

    ```
    shared_ptr
    ```

     

    本身（如

     

    ```
    reset
    ```

    ）仍需加锁；

    ```
    unique_ptr
    ```

     

    无引用计数，所有写操作均需同步。

3. **误区 3**：“`unique_ptr` 的 `get()` 返回裸指针后，线程安全与 `unique_ptr` 无关”

    

    ✅ 正确：

    ```
    get()
    ```

     

    仅返回原始指针，后续对该指针的操作需单独保证线程安全，

    ```
    unique_ptr
    ```

     

    不再参与管理。

------

## 五、总结

| 要点         | 结论                                                         |
| ------------ | ------------------------------------------------------------ |
| 核心规则     | `unique_ptr` 仅保证 “只读无共享修改” 时的线程安全，写操作必须手动加锁 |
| 线程安全边界 | 边界是 `unique_ptr` 实例本身，而非其指向的资源               |
| 实战建议     | 1. 避免多线程共享同一个 `unique_ptr`；2. 若必须共享，用互斥锁保护所有写操作；3. 资源本身的线程安全需单独处理 |

`unique_ptr` 的设计目标是**高效的独占式资源管理**，而非线程安全 —— 线程安全是上层逻辑需解决的问题，而非智能指针的内置特性。

用share_from_this返回的shared_ptr他的引用计数是夺少，如果返回的shared_ptr赋值给另一个shared_ptr这个时候的引用计数又是夺少：```boost::shared_ptr<TestA> GetObj()  {  std::cout << "TestA::GetObj()" << std::endl; boost::shared_ptr<TestA> p = shared_from_this();  return p; }  ```

### 核心结论先明确

`share_from_this()` 返回的 `shared_ptr` 会**复用当前对象已有的 `shared_ptr` 引用计数**，而非创建新的计数；当返回的 `shared_ptr` 赋值给另一个 `shared_ptr` 时，引用计数会**加 1**。

以下结合你的代码示例，分场景详细拆解引用计数的变化逻辑（注：你代码中混用了 `boost::shared_ptr` 和 `std::shared_ptr`，下文统一以 `boost::shared_ptr` 为例，逻辑与 `std::shared_ptr` 完全一致）。

------

## 一、前提：`share_from_this` 的使用条件

`share_from_this` 是 `boost::enable_shared_from_this<TestA>`（或 `std::enable_shared_from_this`）的成员函数，使用的核心前提是：

> 当前 `TestA` 对象**必须已经被至少一个 `shared_ptr` 托管**（即对象的引用计数 ≥ 1），否则调用 `share_from_this()` 会触发未定义行为（通常崩溃）。

因此，我们先构造一个合法的测试场景：

```cpp
#include <boost/shared_ptr.hpp>
#include <boost/enable_shared_from_this.hpp>
#include <iostream>

class TestA : public boost::enable_shared_from_this<TestA> {
public:
    boost::shared_ptr<TestA> GetObj() {
        std::cout << "TestA::GetObj()" << std::endl;
        boost::shared_ptr<TestA> p = shared_from_this(); // 步骤1：调用share_from_this
        return p; // 步骤2：返回shared_ptr
    }

    // 打印当前引用计数（辅助分析）
    void print_ref_count() {
        std::cout << "引用计数：" << boost::shared_from_this().use_count() << std::endl;
    }
};

int main() {
    // 场景1：初始化第一个托管当前对象的shared_ptr
    boost::shared_ptr<TestA> obj = boost::make_shared<TestA>();
    obj->print_ref_count(); // 此时计数：1

    // 场景2：调用GetObj()获取shared_ptr
    boost::shared_ptr<TestA> obj2 = obj->GetObj(); 
    obj->print_ref_count(); // 此时计数：2

    // 场景3：将返回的shared_ptr再赋值给第三个变量
    boost::shared_ptr<TestA> obj3 = obj2;
    obj->print_ref_count(); // 此时计数：3

    return 0;
}
```

------

## 二、逐行拆解引用计数变化

### 1. 初始状态：`obj = boost::make_shared<TestA>()`

- `obj` 是第一个托管 `TestA` 对象的 `shared_ptr`，此时引用计数 = **1**。
- 原理：`make_shared` 为对象分配内存时，同时初始化引用计数为 1。

### 2. 执行 `shared_from_this()` 并赋值给 `p`

```cpp
boost::shared_ptr<TestA> p = shared_from_this();
```

- `share_from_this()` 会返回一个**指向当前对象的新 `shared_ptr`**，但这个新 `shared_ptr` 会复用对象已有的引用计数，因此：
    - 执行后，引用计数 **仍为 1**（`p` 和 `obj` 共享同一个计数）。
- 关键区别：如果直接用 `boost::shared_ptr<TestA>(this)` 构造，会创建**独立的引用计数**（导致双重释放），而 `share_from_this()` 会关联到已有计数，这是其核心价值。

### 3. 返回 `p` 并赋值给 `obj2`

```cpp
boost::shared_ptr<TestA> obj2 = obj->GetObj();
```

- `GetObj()` 返回 `p` 时，`obj2` 会接管 `p` 的所有权（移动 / 拷贝），此时引用计数 **加 1**，变为 **2**。
- 原理：`shared_ptr` 的拷贝 / 移动赋值会操作引用计数 —— 拷贝加 1，移动则转移计数（但此处返回值优化后，本质是拷贝逻辑）。

### 4. 将 `obj2` 赋值给 `obj3`

```cpp
boost::shared_ptr<TestA> obj3 = obj2;
```

- `obj3` 拷贝 `obj2`，引用计数 **再加 1**，变为 **3**。

------

## 三、关键细节补充

### 1. 为什么 `share_from_this()` 不会新增独立计数？

`enable_shared_from_this` 的实现原理是：

- 当对象被第一个 `shared_ptr` 托管时，`enable_shared_from_this` 会**隐式存储一个指向自身的弱引用（weak_ptr）**；
- 调用 `share_from_this()` 时，会通过这个弱引用创建新的 `shared_ptr`，并关联到已有引用计数，而非重新初始化计数。

### 2. 错误场景：未被 `shared_ptr` 托管时调用 `share_from_this()`

如果直接创建栈对象或裸指针对象，调用 `share_from_this()` 会崩溃：

```cpp
TestA a;
a.GetObj(); // 崩溃！a 未被任何shared_ptr托管，share_from_this()无有效计数可关联
```

### 3. 返回值的优化：移动语义不影响计数

`GetObj()` 中返回 `p` 时，编译器会优化为**移动语义**（避免拷贝），但移动操作不会改变引用计数（仅转移所有权，计数仍为 2），只有 “拷贝赋值” 才会让计数加 1。

------

## 四、总结表格

| 操作步骤                     | 引用计数 | 核心原因                          |
| ---------------------------- | -------- | --------------------------------- |
| `obj = make_shared<TestA>()` | 1        | 第一个 shared_ptr 初始化计数      |
| `p = shared_from_this()`     | 1        | 复用已有计数，仅创建新 shared_ptr |
| `obj2 = GetObj()`            | 2        | obj2 拷贝 p，计数加 1             |
| `obj3 = obj2`                | 3        | obj3 拷贝 obj2，计数再加 1        |
| 销毁 obj3                    | 2        | 析构时计数减 1                    |
| 销毁 obj2                    | 1        | 析构时计数减 1                    |
| 销毁 obj                     | 0        | 计数为 0，释放 TestA 对象         |

### 核心规则

- `share_from_this()` 返回的 `shared_ptr` 与原 `shared_ptr` 共享同一引用计数，不会新增独立计数；
- 每一次 “拷贝赋值”`shared_ptr`，引用计数加 1；每一次 `shared_ptr` 析构，计数减 1；
- 计数为 0 时，`shared_ptr` 自动释放托管的对象。

share_from_this()的伪代码例子请给一个

### `enable_shared_from_this` 与 `share_from_this()` 伪代码实现

要理解 `share_from_this()` 的核心逻辑，我们通过**简化的伪代码**还原其底层实现（注：这是逻辑层面的模拟，并非 Boost/STL 的真实源码，但能精准体现核心原理）。

#### 核心原理回顾

`enable_shared_from_this` 的本质是：

1. 当对象被第一个 `shared_ptr` 托管时，隐式存储一个指向自身的 `weak_ptr`（弱引用，不增加计数）；
2. `share_from_this()` 通过这个 `weak_ptr` 创建新的 `shared_ptr`，复用已有引用计数，而非重新初始化。

------

### 完整伪代码实现

```cpp
// 核心：enable_shared_from_this基类
template <typename T>
class EnableSharedFromThis {
protected:
    // 存储指向自身的弱引用（由第一个shared_ptr初始化）
    MockWeakPtr<T> weak_this;

public:
    // share_from_this() 核心实现
    MockSharedPtr<T> share_from_this() {
        // 通过weak_this升级为shared_ptr，复用已有计数
        return weak_this.lock();
    }

    // 保护析构：禁止直接删除子类对象
    ~EnableSharedFromThis() = default;
};


```

------

### 伪代码核心逻辑拆解

#### 1. `EnableSharedFromThis` 基类

- 包含一个 `weak_this` 成员（`MockWeakPtr` 类型），用于**弱引用自身**；
- `share_from_this()` 方法的核心是调用 `weak_this.lock()`，将弱引用升级为 `shared_ptr`，复用已有引用计数。

#### 2. `MockSharedPtr` 构造逻辑

- 当通过 `new T()` 构造第一个 `MockSharedPtr` 时：
    1. 初始化引用计数为 1（堆上分配 `ref_count`，便于共享）；
    2. 检查对象是否继承 `EnableSharedFromThis`，如果是，将 `weak_this` 绑定到当前对象和计数。

#### 3. `share_from_this()` 调用流程

```cpp
obj->GetObj() → share_from_this() → weak_this.lock() → 新建MockSharedPtr（复用ref_count）
```

- 关键点：`lock()` 创建的 `MockSharedPtr` 直接复用已有 `ref_count`，**不重新初始化计数**，仅在拷贝时计数加 1。

#### 4. 计数变化验证

| 操作                            | 引用计数 | 原因                                 |
| ------------------------------- | -------- | ------------------------------------ |
| `obj = MockMakeShared<TestA>()` | 1        | 第一个 shared_ptr 初始化计数         |
| `obj2 = obj->GetObj()`          | 2        | obj2 拷贝返回的 shared_ptr，计数 + 1 |

------

### 关键对比：避免踩坑

如果不通过 `share_from_this()`，直接用 `this` 构造 `shared_ptr`，会创建**独立的计数**，导致双重释放：



```cpp
// 错误写法（伪代码）
MockSharedPtr<TestA> bad_get_obj() {
    return MockSharedPtr<TestA>(this); // 新建计数（值为1），与原计数无关
}

// 调用后：
MockSharedPtr<TestA> obj = MockMakeShared<TestA>(); // 计数1
MockSharedPtr<TestA> obj2 = obj->bad_get_obj();     // 计数1（独立）
// 析构时：obj和obj2各减1，导致对象被delete两次 → 崩溃
```

而 `share_from_this()` 通过 `weak_this` 关联已有计数，彻底避免了这个问题。

------

### 总结

伪代码的核心是体现 `share_from_this()` 的两个关键行为：

1. 依赖 `enable_shared_from_this` 存储的 `weak_ptr` 关联已有引用计数；
2. 生成的新 `shared_ptr` 复用同一计数，而非重新创建，从而保证计数的一致性和对象的安全释放。

真实的 Boost/STL 实现会更复杂（如线程安全、空指针处理、类型推导等），但核心逻辑与上述伪代码完全一致。

当这样返回this指针的时候，返回的shared_ptr赋值给另一个shared_ptr后引用计数是多少 ```shared_ptr<TestA> GetObj() {  std::cout << "TestA::GetObj()" << std::endl; return shared_ptr<TestA>(this); }  ```

### 核心结论（先划重点）

直接返回 `shared_ptr<TestA>(this)` 会创建**独立的引用计数**，而非复用原有计数；当这个返回值赋值给另一个 `shared_ptr` 时，**新计数链的引用计数会 + 1，但与原对象的计数链完全无关**，最终会导致对象被双重释放（未定义行为）。

以下结合代码场景拆解计数变化，对比 `share_from_this()` 突出问题本质。

------

## 一、完整测试场景（含计数打印）

先给出可运行的测试代码（以 `std::shared_ptr` 为例，`boost::shared_ptr` 逻辑完全一致）：

```cpp
#include <memory>
#include <iostream>

class TestA {
public:
    std::shared_ptr<TestA> GetObj() {
        std::cout << "TestA::GetObj()" << std::endl;
        return std::shared_ptr<TestA>(this); // 直接用this构造shared_ptr
    }

    // 析构函数：验证是否被重复释放
    ~TestA() {
        std::cout << "TestA 析构" << std::endl;
    }
};

int main() {
    // 步骤1：创建第一个托管TestA的shared_ptr（计数链1）
    std::shared_ptr<TestA> obj1 = std::make_shared<TestA>();
    std::cout << "obj1 引用计数：" << obj1.use_count() << std::endl; // 输出 1

    // 步骤2：调用GetObj()返回this构造的shared_ptr（计数链2）
    std::shared_ptr<TestA> obj2 = obj1->GetObj();
    std::cout << "obj1 引用计数：" << obj1.use_count() << std::endl; // 仍为 1（计数链1无变化）
    std::cout << "obj2 引用计数：" << obj2.use_count() << std::endl; // 输出 1（计数链2初始值）

    // 步骤3：将obj2赋值给obj3（计数链2的计数+1）
    std::shared_ptr<TestA> obj3 = obj2;
    std::cout << "obj2 引用计数：" << obj2.use_count() << std::endl; // 输出 2
    std::cout << "obj3 引用计数：" << obj3.use_count() << std::endl; // 输出 2

    return 0;
}
```

### 运行结果（关键问题）

```plaintext
obj1 引用计数：1
TestA::GetObj()
obj1 引用计数：1
obj2 引用计数：1
obj2 引用计数：2
obj3 引用计数：2
TestA 析构
TestA 析构 // 重复析构！程序大概率崩溃
```

------

## 二、逐行拆解引用计数变化

### 1. 初始状态：`obj1 = make_shared<TestA>()`

- `obj1` 是第一个托管 `TestA` 对象的 `shared_ptr`，创建**计数链 1**：
    - 引用计数 = 1（对象地址：`0x123`，计数地址：`0x456`，值 = 1）；
- 此时对象仅被 `obj1` 管理，计数链 1 是唯一的计数来源。

### 2. 执行 `return shared_ptr<TestA>(this)`

```cpp
return std::shared_ptr<TestA>(this);
```

- `this` 指向的是同一个 `TestA` 对象（地址 `0x123`），但 `shared_ptr<TestA>(this)` 会**新建一个独立的计数链 2**：
    - 计数地址：`0x789`，初始值 = 1；
    - 计数链 1（`0x456`）完全不受影响，仍为 1；
- 核心问题：**同一个对象被两个独立的 `shared_ptr` 计数链管理**，彼此无关联。

### 3. 赋值给 `obj2`：`obj2 = obj1->GetObj()`

- `obj2` 接管返回的 `shared_ptr`，属于计数链 2，因此：
    - 计数链 2 的值 = 1（无拷贝，仅移动 / 直接赋值）；
    - 计数链 1 仍为 1（与 obj2 无关）。

### 4. 赋值给 `obj3`：`obj3 = obj2`

- `obj3` 拷贝 `obj2`，属于计数链 2 的操作，因此：
    - 计数链 2 的值 +=1 → 变为 2；
    - 计数链 1 仍为 1（全程无变化）。

### 5. 程序结束：析构阶段（致命问题）

- `obj3` 先析构：计数链 2 的值 -=1 → 1；
- `obj2` 再析构：计数链 2 的值 -=1 → 0 → 触发 `delete this`（释放对象 `0x123`）；
- `obj1` 最后析构：计数链 1 的值 -=1 → 0 → 再次触发 `delete this`（重复释放对象 `0x123`）；
- 结果：未定义行为（通常是程序崩溃、内存损坏）。

------

## 三、关键对比：`this` 构造 vs `share_from_this()`

| 场景                | 引用计数链     | 计数变化             | 安全性               |
| ------------------- | -------------- | -------------------- | -------------------- |
| `shared_ptr(this)`  | 多个独立计数链 | 各计数链独立增减     | 极不安全（双重释放） |
| `share_from_this()` | 唯一计数链     | 所有操作复用同一计数 | 安全（计数一致）     |

------

## 四、总结

1. **返回 `shared_ptr(this)` 时的计数**：

    - 新创建的 `shared_ptr` 计数 = 1（独立计数链）；
    - 原对象的已有计数链完全不受影响。

2. **赋值给另一个 `shared_ptr` 后的计数**：

    - 新计数链的值 +=1（变为 2）；
    - 原计数链仍保持不变。

3. **核心风险**：

    

    同一个对象被多个独立的

    ```
    shared_ptr
    ```

    计数链管理，最终必然导致双重释放，这是 C++ 中典型的 “智能指针误用” 错误。

### 修复方案

必须让 `TestA` 继承 `enable_shared_from_this<TestA>`，并使用 `share_from_this()` 替代 `shared_ptr(this)`：

```cpp
class TestA : public std::enable_shared_from_this<TestA> {
public:
    std::shared_ptr<TestA> GetObj() {
        std::cout << "TestA::GetObj()" << std::endl;
        return shared_from_this(); // 复用已有计数链
    }
};
```

此时所有 `shared_ptr` 共享同一计数链，赋值后计数正常累加，析构时仅释放一次，完全安全。