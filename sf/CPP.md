

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
2. 隐藏override: 父类子类，同名，同参数的虚函数，操作覆盖      ***virtual, 可以用基类指针访问子类方法实现多态***
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
（4）基类函数必须有virtual 关键字。

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

\2.   若基类析构函数为非虚，则基类析构为静态，编译器在对象delete的时候静态链接父类析构函数。

\3.   若基类析构函数为虚函数，则基类析构为动态，编译器会把基类析构函数放入虚表中，在对象释放的地方会去虚表找析构函数（编译器会根据virtual决定是动态链接还是静态链接），在子类构造的时候子类会把自己的析构函数在虚表里覆盖基类的析构。

\4.   析构函数是链式循环调用的，即只要调用子类析构，在子类析构中会自动调用父类析构。因此虚表中只要记录最下层子类的析构函数，并在对象释放的时候调用这个子类析构函数，就会链式的调用所有基类的析构

\5.   没有visual 声明就是静态编译地址，直接调用函数的位置，有visual声明就是动态的调用，调用的时候会查找虚表，根据虚表调用函数


---
# 10 explicit

避免Test2 t3 = 14;这种情况出现，容易出现错误。



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

const 成员变量只能在类内声明、定义，在构造函数初始化列表中初始化。
const 成员变量只在某个对象的生存周期内是常量，对于整个类而言却是可变的，因为类可以创建多个对象，不同类的 const 成员变量的值是不同的。因此不能在类的声明中初始化 const 成员变量，类的对象还没有创建，编译器不知道他的值。

-   const 成员函数：

不能修改成员变量的值，除非有 mutable 修饰；只能访问成员变量。
不能调用非常量成员函数，以防修改成员变量的值。

`const` 相比define的优点：

-   有数据类型，在定义式可进行安全性检查。
-   可调式。
-   占用较少的空间。


# 14 inline
1. inline 关键字应该在函数定义中，不是在声明
2. inline函数应该都放在头文件中，连同函数的定义，单独的函数声明不起作用
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
-   volatile 对编译器的影响：使用该关键字后，编译器不会对相应的对象进行优化，即不会将变量从内存缓存到寄存器中，防止多个线程有可能使用内存中的变量，有可能使用寄存器中的变量，从而导致程序错误。
-   volatile 关键字和 const 关键字可以同时使用，某种类型可以既是 volatile 又是 const ，同时具有二者的属性。
# 20 虚函数表
-   虚函数表和类绑定，虚表指针和对象绑定。
-   当子类没有虚函数，但是父类存在虚函数的时候，子类依旧会新建一个虚表，虚表里面存放的是父类的虚函数指针。
-   当子类没有虚函数时，子类对象的虚指针指向基类的虚函数表，子类没有虚函数表。但是当子类重写基类的虚函数时或者添加了一个新的虚函数时，子类自己会拥有一个属于自己的虚函数表，表中会存放基类虚函数的地址。

# 21 RTTI
RTTI(Run Time Type Identification)即通过运行时类型识别，程序能够使用基类的指针或引用来检查着这些指针或引用所指的对象的实际派生类型。
RTTI提供了两个非常有用的操作符：typeid和dynamic_cast。

-   typeid操作符，返回指针和引用所指的实际类型；
-   dynamic_cast操作符，将基类类型的指针或引用安全地转换为其派生类类型的指针或引用。

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