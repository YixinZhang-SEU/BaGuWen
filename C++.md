> 待解决问题
>
> 1. 各个数据类型占用内存（字节） 
> 1. 特化？
> 1. `RAII` 模板类

## 各数据类型占用的字节

| 数据类型          | 32位系统占用字节 | 64位系统占用字节         |
| ----------------- | ---------------- | ------------------------ |
| char              | 1                | 1                        |
| char*（指针变量） | 4                | <font color=red>8</font> |
| short int         | 2                | 2                        |
| int               | 4                | 4                        |
| unsigned int      | 4                | 4                        |
| float             | 4                | 4                        |
| double            | 8                | 8                        |
| long              | 4                | <font color=red>8</font> |
| long long         | 8                | 8                        |
| unsigned long     | 4                | <font color=red>8</font> |



# b站C++

## 指针

指针变量简称指针，是一种特殊的变量，专门用于存放变量在内存中的起始地址。 

对指针赋值：`指针=&变量名`

在64位的操作系统中，不管是什么类型的指针，占用的内存都是8字节，32位是4个字节。  

```c++
int a = 3;
int *p = &a;
cout << a << " " << *p << endl;		// 3 3
*p = 8;		// 相当于a = 8
cout << a << " " << *p << endl;		// 8 8
// 多个指针可以指向同一个变量
```

### 常量指针

`const int *`  不能通过解引用的方法修改内存地址中的值

```c++
int a = 3;
int b = 6;
const int *p = &a;
*p = 3;		// 错误！
a = 6;		// ok
p = &b;		// ok
```

指针作为函数形参时加const约束，表示在函数中不能通过对形参解引用的方式修改实参值

### 指针常量

`int *const`  指向不能改变，因此必须要初始化，否则无意义。

也叫引用 

```c++
int a = 3;
int b = 6;
int *const p = &a;
p = &b;		// 错误！
*p = 6;		// ok
```

### 常量指针常量

指向不能改变，也不能解引用修改指向的值。

也叫常引用。

### void*

表示接受任意数据类型的指针。

注意：

- 不能对void*指针直接解引用，而是需要先转换成其它类型的指针
- 把其他类型指针赋值给void***不需要**转换
- 把void*型赋值给其他类型指针**需要**转换

















https://blog.csdn.net/qq_19887221/article/details/126265302






#### 内存分区

`C++` 程序使用的内存分区一般包括：栈、堆、全局/静态存储区、常量存储区、代码区。

**栈**  存放函数的局部变量、函数参数、返回地址等。进程退出时，操作系统才会对栈空间进行回收。

**堆**  动态申请的内存空间，由 `malloc` 函数或者 `new` 函数分配的内存块，可以在程序运行周期内随时进行申请和释放，如果进程结束后还没有释放，操作系统会自动回收。

**全局区/静态存储区**  主要为 `.bss` 段和 `.data` 段，存放全局变量和静态变量，程序运行结束操作系统自动释放.

**常量存储区**  `.rodata ` 段，存放的是常量，不允许修改，程序运行结束自动释放。

**代码区**  `.text` 段，不可修改，但可以执行。



内存泄漏

> https://zhuanlan.zhihu.com/p/523628871?utm_id=0#:~:text=%E6%97%A5%E5%BF%97,%E5%88%86%E6%9E%90%E5%92%8C%E5%AE%9A%E4%BD%8D%E3%80%82



#### 变量作用域

全局变量extern、静态全局变量static（不用文件中要重新定义）、局部变量、静态局部变量



#### 为什么静态成员函数不能是const

这是C++的规则，const修饰符用于表示函数不能修改成员变量的值，该函数必须是含有this指针的类成员函数，函数调用方式为thiscall，而类中的static函数本质上是全局函数，调用规约是__cdecl或__stdcall,不能用const来修饰它。一个静态成员函数访问的值是其参数、静态数据成员和全局变量，而这些数据都不是对象状态的一部分。而对成员函数中使用关键字const是表明：函数不会修改该函数访问的目标对象的数据成员。既然一个静态成员函数根本不访问非静态数据成员，那么就没必要使用const了。



#### 智能指针

`make_unique` 在 C++ 14 以后才被加入到标准的 C++ 中，`make_shared` 则是 C++ 11 中加入的。在 「《Effective Modern C++》」 学习笔记之条款二十一：优先选用 `std::make_unique` 和 `std::make_shared`,而非直接 `new`。

```c++
auto upw(make_unique<Widget>());
```



#### 编译与链接

编译预处理、编译、汇编、链接

静态编译：程序被执行时，该程序运行时所需要的全部代码都会被装入到该进程的虚拟地址空间中。缺点是浪费空间，更新困难（如果目标文件进行了更新操作，就需要重新进行编译链接生成可执行程序），优点就是执行的时候运行速度快，因为可执行程序具备了程序运行的所有内容。

动态编译：程序被放到动态链接库或共享对象的某个目标文件中，链接程序只是在最终的可执行程序中记录了共享对象的名字等一些信息，最终生成的 ELF文件中并不包含这些调用程序二进制指令。在程序执行时，当需要调用这部分程序时，操作系统会从将这些动态链或者共享对象进行加载。优点是节省内存、更新方便，缺点是有一定的性能损失。



#### 大端与小端

```c++
bool byteorder_check() {
    int a = 1;
    return (*(char *)&a); /* 1 为小端机，0 为大端机 */
}

```



#### 防止内存泄漏

$\bullet$ 在 C++ 中需要将基类的析构函数定义为虚函数；
$\bullet$ 遵循 RAII（Resource acquisition is initialization）原则：在对象构造时获取资源，在对象生命期控制对资源的访问使之始终保持有效，最后在对象析构的时候释放资源；
$\bullet$ 尽量使用智能指针；
$\bullet$ 有效引入内存检测工具；



#### C++版本

`C++11`  auto自动类型推导（编译期间）、lambda表达式、智能指针、右值引用、constexpr常量表达式、initializer list初始化列表、nullptr

```c++
// lambda表达式
int main()
{
    int a = 10;
    auto f = [&a](int x)-> int {
        a = 20;
        return a + x;
    };
    cout<<a<<endl; // 10
    cout<<f(10)<<endl; // 30
    cout<<a<<endl; // 20
    return 0;
}

/*
    
这段代码是一个 C++ 的 Lambda 表达式示例，用于演示 Lambda 表达式与捕获列表的使用。

1. 首先，在 main() 函数中定义了一个整型变量 a 并赋值为 10。
2. 然后，使用 Lambda 表达式创建了一个函数对象 f，这个 Lambda 表达式包含一个捕获列表 [&a] 和一个函数体。
3. [&a] 表示通过引用捕获了变量 a。这意味着 Lambda 表达式中的函数体可以访问并修改变量 a，并且变量 a 的修改会影响到外部的 main() 函数中的 a。
4. (int x) -> int 表示 Lambda 表达式的参数列表和返回类型。在这里，Lambda 表达式接受一个整型参数 x，并返回一个整型值。
5. Lambda 表达式的函数体中，首先将变量 a 的值修改为 20，然后将修改后的 a 的值与参数 x 相加，并作为结果返回。
6. 在 main() 函数中分别输出了 a 的值和调用 Lambda 表达式 f 的结果。

Lambda 表达式是 C++11 引入的特性，它允许我们在代码中定义匿名的函数对象，方便地进行局部的函数定义，并可以捕获外部作用域的变量。在这个示例中，通过捕获 a，我们可以在 Lambda 表达式中修改外部作用域中的变量。
*/
```

`c++17`  std::basic_string_view

```c++
string s = "123456789";
std::string_view sv(s.c_str());
cout<<sv<<endl;
for (auto & ch : sv) {
    cout << ch << ' ';
}
for (auto it = sv.crbegin(); it != sv.crend(); ++it) {
    cout << *it << ' ';
}
cout<<sv.find("345")<<endl; // 2
```



#### const

- `const` 修饰指针指向的内容，则指针指向的内容不可变，但是指针本身的内容可以改变。

```c++
int x = 0;
int *q = &x;
const int *p = &x;
*p = 10; // error
p = q; // OK
```

- `const` 修饰指针，则指针为不可变量，指针指向的内容可以变，但指针本身不能变

```c++
int a = 8;
int* const p = &a; // 指针为常量
*p = 9;  // OK
int  b = 7;
p = &b; // error
```

- `const` 修饰指针和指针指向的内容，则指针和指针指向的内容都为不可变量。

```c++
int a = 8;
const int * const  p = &a;
```



#### 函数重载、重写、隐藏区别

- 函数重载：参数列表不同。只有返回值不同不可以算作重载函数
- 函数隐藏：指派生类的函数屏蔽了与其同名的基类函数，只要是与基类同名的成员函数，不管参数列表是否相同，基类函数都会被隐藏。
- 函数重写（覆盖）：
- 指派生类中存在重新定义的函数。函数名、参数列表、返回值类型都必须同基类中被重写的函数一致，只有函数体不同。派生类调用时会调用派生类的重写函数，不会调用被重写函数。重写的基类中被重写的函数必须有 `virtual` 修饰。



#### 虚继承

https://blog.csdn.net/longlovefilm/article/details/80558879



#### 左值、右值

- 非常量左值引用可以绑定非常量左值，`不可以`绑定常量左值和右值
- 常量左值引用可以绑定非常量左值，常量左值和右值

  ##### 右值引用

  实现了转移语义 （Move Sementics）和精确传递 （Perfect Forwarding），&& 作为右值引用的声明符。右值引用必须绑定到右值的引用，通过 && 获得。右值引用只能绑定到一个将要销毁的对象上，因此可以自由地移动其资源。

  右值引用两个主要功能：

  - 消除两个对象交互时不必要的对象拷贝，节省运算存储资源，提高效率。
  - 能够更简洁明确地定义泛型函数。

```c++
#include <iostream>
using namespace std;

typedef int&  lref;
typedef int&& rref;

void fun(int&& tmp) 
{ 
    cout << "fun rvalue bind:" << tmp << endl; 
} 

void fun(int& tmp) 
{ 
    cout << "fun lvalue bind:" << tmp << endl; 
} 

int main() 
{ 
    int a = 11; 
	int &b = a;
	int &&c = 100;
	fun(static_cast<lref &&>(b));
	fun(static_cast<rref &&>(c));
	fun(static_cast<int &&>(a));
	fun(static_cast<int &&>(b));
	fun(static_cast<int &&>(c));
    return 0;
}
/*
fun lvalue bind:11
fun rvalue bind:100
fun rvalue bind:11
fun rvalue bind:11
fun rvalue bind:100
*/

// 最后一段代码，前面两个展示的是forward的原理，根据传递进来的是左值还是右值对参数进行静态转换，后三个是move的原理，先去引用，然后在静态转换为右值引用
```



#### 指针

##### 指针运算

- 两个同类型指针可以比较大小；
- 两个同类型指针可以相减；
- 指针变量可以和整数类型变量或常量相加；
- 指针变量可以减去一个整数类型变量或常量；
- 指针变量可以自增，自减；

```c++
int a[10];
int *p1 = a + 1; // 指针常量相加
int *p2 = a + 4;
bool greater = p2 > p1; // 比较大小
int offset = p2 - a; // 相减
p2++; // 自增
p1--; // 自减
```

- 指向常量对象的指针：常量指针，`const` 修饰表示指针指向的内容不能更改。

```c++
const int c_var = 10;
const int * p = &c_var;
cout << *p << endl;
```

- 指向函数的指针：函数指针。

```c++
#include <iostream>
using namespace std;

int add(int a, int b){
    return a + b;
}

typedef int (*fun_p)(int, int);

int main(void)
{
    fun_p fn = add;
    cout << fn(1, 6) << endl;
    return 0;
}
```

- 指向对象成员的指针，包括指向对象成员函数的指针和指向对象成员变量的指针。
  特别注意：定义指向成员函数的指针时，要标明指针所属的类。

```c++
#include <iostream>

using namespace std;

class A
{
public:
    int var1, var2; 
	static int x;
	static int get() {
		return 100;
	}

    int add(){
        return var1 + var2;
    }
};



int main()
{
    A ex;
    ex.var1 = 3;
    ex.var2 = 4;
    int *p = &ex.var1; // 指向对象成员变量的指针
    cout << *p << endl;

    int (A::*fun_p)();
	int (*fun_q)();
    fun_p = &A::add; // 指向对象非静态成员函数的指针 fun_p  非静态成员要用&
	fun_q = A::get; // 指向对象静态成员函数的指针 fun_q
	cout << (ex.*fun_p)() << endl;
    cout << (*fun_q)() << endl;
    return 0;
}
```



#### 指针和引用

```c++
int num = 10;
int *ptr = &num;            // *ptr = num是错误的！
cout << *ptr << endl;       // 10
cout << ptr << endl;        // 0x61fe0c（地址）
int &ref = num;
cout << ref << endl;        // 10
cout << &ref << endl;       // 0x61fe0c（地址）
```



#### 常量指针和指针常量

##### 常量指针

本质是指针，它指向的对象是常量。特点是 `const` 在 `*` 的左侧。
指向的对象不能变化，但是指针本身可以被重新赋值。

```c++
const int c_var1 = 8;
const int c_var2 = 8;
const int *p = &c_var1; 	// 常量指针
*p = 6;			// error, *p是常量，但p不是常量
p = &c_var2;	// ok
```

##### 指针常量

本质是常量，这个常量的值是一个指针。特点是 `const` 在 `*` 的右侧。

```c++
int var = 10, var1;
int * const c_p = &var;	// 指针常量
c_p = &var1;	// error, c_p是常量，但*p不是常量
*c_p = 12;		// ok
```

##### 常量指针常量

指向常量的指针常量，指针的指向不可修改，指针所指的内存区域中的值也不可修改。

```c++
int var, var1;
const int * const c_p = &var;
c_p = &var1; // error: assignment of read-only variable 'c_p'
*c_p = 12; // error: assignment of read-only location '*c_p
```

##### 总结

```c++
int ** const p;  // p 是一指针常量，它是一个指向指针的指针常量；
int * const * p; // p 是一个指针，它是一个指向指针常量的指针；
int const ** p;  // p 是一个指针，它是一个指向常量的指针的指针；
int * const * const p; // p 是一指针常量，它是一个指向指针常量的指针常量；
```



#### 函数指针

```c++
#include <iostream>
using namespace std;
int fun1(int tmp1, int tmp2)
{
  return tmp1 * tmp2;
}
int fun2(int tmp1, int tmp2)
{
  return tmp1 / tmp2;
}

int main()
{
  int (*fun)(int x, int y); 
  fun = fun1; // ok
  fun = &fun1; // ok 两种写法均可以
  cout << fun(15, 5) << endl; 
  fun = fun2;
  cout << fun(15, 5) << endl; 
  cout<<sizeof(fun1)<<endl; // error
  cout<<sizeof(&fun1)<<endl;
  return 0;
}
/*
运行结果：
75
3
*/
```



#### 值传递、指针传递、引用传递

```c++
#include <iostream>
using namespace std;

void fun1(int tmp){ // 值传递
    cout << &tmp << endl;
}

void fun2(int * tmp){ // 指针传递
    cout << tmp << endl;
}

void fun3(int &tmp){ // 引用传递
    cout << &tmp << endl;
}

int main()
{
    int var = 5;
    cout << "var 在主函数中的地址：" << &var << endl;

    cout << "var 值传递时的地址：";
    fun1(var);

    cout << "var 指针传递时的地址：";
    fun2(&var);

    cout << "var 引用传递时的地址：";
    fun3(var);
    return 0;
}

/*
运行结果：
var 在主函数中的地址：0x23fe4c
var 值传递时的地址：0x23fe20
var 指针传递时的地址：0x23fe4c
var 引用传递时的地址：0x23fe4c
*/
```



#### tips

在 `C++ STL` 中，容器 `vector`、`deque` 提供随机访问迭代器，`list` 提供双向迭代器，`set` 和 `map` 提供向前迭代器。













