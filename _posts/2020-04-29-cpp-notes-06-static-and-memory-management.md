---
title: 【C++笔记】06 C预处理器、作用域、static、const以及内存管理
tags: C++笔记
layout: article
key: CPP_NOTES_06
mode: immersive
header:
  theme: dark
article_header:
  type: cover
  image:
    src: assets/images/cover2.jpg
---


## 1 C预处理器

C语言的预处理主要有三个方面的内容：
- 宏定义与宏替换
- 文件包含
- 条件编译

### 1.1 宏定义与宏替换

宏语句一般以`#define`开头，分为两种：符号常量的宏定义和带参数的宏定义。

- 符号常量的宏定义与宏替换

  ```c
  #define 标识符 字符串
  ```

  其中标识符为宏名称，**宏定义末尾不加分号**。

  {:.info}

  注：由于预处理是在编译之前的处理，而编译工作的任务之一就是语法检查，故**预处理是不做语法检查的**。且宏定义**不分配内存**，变量定义才会分配内存。

- 带有参数的宏定义与宏替换

  ```c
  #define 标识符（参数列表）字符串
  ```

  对带有参数的宏定义进行宏替换时，不仅对宏标识符做字符替换，还必须做参数的替换。

为了避免宏替换时发生错误，**宏定义中宏参数应加上括号**。如：

```c
#define FUN(x) (x*x)
//则
FUN(a+b);//相当于a+b*a+b，与期望中的(a+b)*(a+b)不同
//正确的宏定义写法应为：
#define FUN(x) ((x)*(x))
```

宏定义可以嵌套使用：

```c
#define SQ(x) ((x)*(x))
#define CUBE(x) (SQ(x)*(x))
```

宏替换的本质很简单——**文本替换**。
关于宏定义与宏替换需要**注意以下几点**：

- 宏名一般用大写，**宏名和参数的括号间不能有空格**，宏定义末尾不加分号；

- 宏替换只作替换，**不做语法检查，不做计算，不做表达式求解**；

- 宏替换在编译前进行，不分配内存，**函数调用在编译后程序运行时进行，并且分配内存**；

- 函数只有一个返回值，利用宏则可以设法得到多个值；

- **宏替换使源程序变长**，函数调用不会；

- **宏替换不占运行时间，只占编译时间**，函数调用占运行时间（分配内存、保留现场、值传递、返回

{:.info}

注：**应尽量少用宏替换**，在C++中，宏替换实现的符号常量功能由`const`、`enum`代替，带参数的宏替换可由模板内联函数代替。

**用宏求结构体的内存偏移地址**：

```c
#define OFFSET(type, field) ((int)&(((type*)0)->field))
struct S{
    int a;
    char c;
    int b;
};
cout << OFFSET(S,c);//输出4
```



### 1.2 文件包含

#include接受以下两种形式：

```c
#include <head_file>
#include "head_file"
```

如果头文件名包含在<>中，则为标准头文件，编译器将会在**预定义的位置集合**中查找该头文件，这些预定义的位置可以通过设置查找路径环境或者通过命令行选项来修改；如果头文件名包含在""中，则为非系统文件，通常**在源文件所在的路径**查找。

### 1.3 条件编译

提供条件编译措施使同一源程序可以根据不同编译条件（参数）产生不同的目标代码，其作用在于便于调试和移植。条件编译控制语句的形式有：

```c
#if/ifdef/ifndef
#elif
#else
#endif
```

预处理器变量有两种状态：已定义或未定义。`#define`接受指示一个名字并定义该名字为预处理器变量。



## 2 全局变量与局部变量

### 2.1 全局变量

全局变量也称外部变量，是在函数外部定义的变量，不属于任何一个函数，只属于源程序文件，其**作用域是整个源程序**。

在不同文件中引用一个已经定义过的全局变量，可以用引用头文件的方式，也可以用`extern`关键字：

```c
//file1.cpp
int counter;//定义counter
//file2.cpp
extern int counter;//使用file1中的counter
counter++;
```

{:.info}

注：**使用引用头文件方式来引用**某个在头文件中声明的全局变量时，如果这个变量名写错了，会在**编译期间**报错；而**使用`extern`方式引用**时，如果这个变量名写错了，编译期间不会报错，会在**连接期间**报错。

### 2.2 局部变量

局部变量指在程序中，只在**特定过程或函数**中可以访问的变量。在C等面向过程语言中，局部变量可以和全局变量重名，但局部变量会屏蔽全局变量，在函数内引用这个变量时，会用到同名的局部变量，而不会用到全局变量。

在同一个文件中，当局部变量屏蔽了全局变量，而又想要使用全局变量时，有**两种方法**：

1. 使用作用域操作符`::`;

2. 使用`extern`。

   ```c
   int counter = 3;
   
   int main()
   {
       static int count = 4;
       counter++;
       if(i%2 == 1)
       {
           //::count++;//方法1
           extern int count;//方法2
           count++;
       }
       return 0;
   }
   ```



## 3 static

### 3.1 static的作用

1. **隐藏**。当我们同时编译多个文件时，所有未加static前缀的全局变量和函数都具有全局可见性，而如果加了static，就会**对其他源文件隐藏**。利用这一特性**可以在不同的文件中定义同名函数和同名变量**，而不必担心命名冲突。

   {:.info}

   注：static可以用作函数和变量的前缀，对于函数来讲，static的作用仅限于隐藏，而对于变量，static还有下面两个作用。

2. **默认初始化为0，包括未初始化的全局静态变量与局部静态变量**（未初始化的全局变量与未初始化的静态变量是存储在同一块区域BSS段，BSS段中内存的所有字节默认值都是0x00；初始化的静态变量存储在data段中）。

3. **保持局部变量内容的持久**。函数内的局部变量只有当函数被调用时存在，退出函数时就消失，但静态局部变量虽然在函数内定义，但静态局部变量始终存在着，它的生存期为整个源程序，但**需注意的是它的作用域仍与局部变量相同，即只能在定义该变量的函数内使用该变量，退出函数时，尽管该变量依然存在，但<font color="#FF0000">不能使用</font>**。

### 3.2 类中static的作用
表示属于一个类而不是属于此类的任何特定对象的变量和函数，static成员可以是函数或数据，都独立于类类型的对象而存在。

#### 3.2.1 静态数据成员

- static数据成员独立于该类的任意对象而存在；每个static数据成员的值为该类的**所有实例共享**。

- 静态数据成员和普通数据成员一样遵从public，protected，private访问规则。

- 静态数据成员在存储在全局（静态）存储区。**静态数据成员定义时要分配空间，所以<font color="#FF0000">不能</font>在类声明中定义**。static数据成员必须在类定义体的外部定义，通常在外部定义时才初始化，即在类定义体内对静态变量赋初值是**错误**的。

  {:.info}

  注：基本整型const static数据成员可以在类的定义体内进行初始化，在类定义体中初始化时，该数据成员**仍必须在类的定义体外进行定义**，只不过定义时，不再需要初始化。

- 类中数据成员的布局情况是：

  1. 非静态成员在类对象中的排列顺序和声明顺序一致，任何在其中间声明的静态成员都不会被放进对象布局中；

  2. **静态成员存放在程序的全局（静态）存储中，和个别类对象无关**。

     C++中，在同一个访问块private、public、protected等区段中，成员的排列只需要符合较晚出现的成员在类对象中有较高的地址即可，并**不一定要连续排列**。什么东西可能会介于被声明的members之间呢？比如members的边界调整时需要填充的一些字节等。

#### 3.2.2 静态成员函数

静态成员函数与静态数据成员一样，都是类的内部实现，属于类定义的一部分，它为类服务而不是为某一个类的具体对象服务。

- 普通成员函数总是具体的属于某个类的具体对象，所以普通的成员函数一般都隐含了一个this指针，指向类的对象本身。静态成员函数由于不与任何的对象相关联，因此**不具有this指针**。因而它**无法访问属于类对象的非静态数据成员，也无法访问非静态成员函数**，它只能调用其余的静态成员函数与访问静态数据成员。

- 由于没有this指针的额外开销，因此**静态成员函数与类的非静态成员函数相比速度上会有少许的增长**。

- static成员不是任何对象的组成部分，所以**static成员函数不能被声明为const**。将成员函数声明为const就是承诺不会修改该函数所属的对象，而static成员函数不属于任何对象。

- static成员函数不能声明为虚函数、volatile。

使用static成员变量而不是全局变量有三个**优点**：

1. static成员的名字是在类的作用域中，因此可以避免与其他类的成员或全局对象名字冲突。
2. 可以实施封装，static成员可以是私有成员，而全局对象不可以。
3. 通过阅读程序容易看出static成员是特定类关联的。这种可见性可清晰地显示程序员的意图。



## 4 const

### 4.1 常量

C++中，const限定符将一个对象转换成一个常量。**常量在定义后就不能被修改，所以定义时必须初始化。**

在全局作用域中定义非const变量时，它在整个程序中都可以访问。与其他变量不同，除非特别说明，在全局作用域中定义的const变量，为定义该对象的文件的**局部变量**，此变量只存在于那个文件中，不能被其他文件访问。通过指定const变更为extern，就可以在整个程序中访问const对象。

**const在C/C++中的区别**：

1. C中const指一个不能被改变的普通变量。C编译器不能把const视为一个编译期间的常量，如使用const变量声明一个数组在C中是错误的，而在C++中该写法是正确的：

   ```c
   const int bufsize=100;
   int buf[bufsize];
   ```

2. C默认const是外部连接的；C++默认const是内部连接的，如果要改成外部连接，必须使用extern。

   ```c
   const int size;//C中正确,C++中编译报错必须初始化
   extern const int size;//C++中通过
   ```

   {:.info}

   注：在C语言中使用限定符const不是很有用，C迫使程序员在预处理器里使用`#define`。

**用const替代`#define`的值替换功能**：

C++中使用const比使用`#define`有更多的**优点**：

1. **const常量有数据类型，而宏常量没有数据类型**。编译器可以对前者进行类型安全检查。而对后者只进行字符替换，没有类型安全检查，并且在字符替换时可能会产生意料不到的错误；
2. 使用常量可能比使用`#define`导致产生**更小的目标代码**，这是因为预处理器“盲目地将宏名长替换为其代替的值”可能导致目标代码出现**大量的备份**，但常量就不会出现这种情况。
   3）同时const还可以执行**常量折叠**（常量折叠是在编译时间简单化常量表达的一个过程，简单来说就是将常量表达式计算求值，并用求得的值来替换表达式，放入常量表），也就是说，编译器在编译时可以通过必要的计算把一个复杂的常量表达式缩减成简单的。

### 4.2 指针和const修饰符

#### 4.2.1 指向const的指针

指向const对象，不允许用指针来改变其所指的const值。

```c
const double *cptr; //等价于double const *cptr;
```

理解为：cptr是一个指针，指向一个const double数据。这里不需要初始化，因为cptr可以指向任何东西，但所指的东西不能被改变。

#### 4.2.2 const指针

使指针本身成为一个const指针，必须把const标明的部分放在*的**右边**。

```c
double d=1.0;
double* const cptr=&d;
```

因为指针本身是const指针，编译器要求给它一个初始化值，这个值在指针寿命期间不变。然而要改变它所指向的值是可以的，可以写`*cptr = 2.0;`。

将一个const指针变为指向一个const对象：

```c
double d = 1.0;
const double * const x=&d;//等价于double const * const x=&d;
```

现在指针和对象都不能改变。

**不同形式的指针含义**：

```c
double* ptr=&value;//ptr是一个指向double类型的指针，ptr的值可以改变，ptr所指向的value的值也可以改变；
const double* ptr=&value;//ptr是一个指向const double类型的指针，ptr的值可以改变，不能通过ptr改变value的值；
double* const ptr=&value;//ptr是一个指向double类型的const指针，ptr的值不可以改变，可以通过ptr改变value的值；
const double* const ptr=&value;//ptr是一个指向const double类型的const指针，ptr的值不可以改变，也不能通过ptr改变value的值。
```

### 4.3 修饰函数参数与返回值

#### 4.3.1 const修饰参数

如果函数是以值传递的，可用const限定函数参数：

```c
void fun(const int i);
```

这将明确告诉编译器i的值在函数体内不会也无法改变。值传递时，这种约定对函数调用者意义不大，但若是传地址，则应尽可能用const修饰（除非此参数确实需要在函数内修改）。如果不这样做，就使得指向const的指针不能做实参，如：

```c
int fun(int* i);
const int a = 1;
fun(&a);//编译报错
```

#### 4.3.2 const修饰返回值

const修饰返回值常用在处理用户定义的类型时。

函数除了返回值类型外还可以返回指针。函数不能返回指向局部栈变量的指针，这是因为在函数返回后它们是无效的，而且栈也被清理了。可返回的指针是指向堆中分配的存储空间的指针或指向静态存储区的指针，在函数返回后仍然有效。

### 4.4 const在类中的应用

#### 4.4.1 const成员函数

```c
class base{
    void func() const;
};
```

func函数声明末尾的const改变了隐含的this形参的类型，使this形参指向的对象为const类型（this本身类型为`base* const`，函数声明末尾加上const后，this的类型为`const base* const`，即this指向的对象也为const）。const成员函数不能修改调用该函数的对象（mutable成员除外）。

const实施于成员函数的目的，是为了确保该成员函数可作用于const对象身上。const对象、指向const对象的指针或引用只能调用其const成员函数，如果尝试用它们来调用非const成员函数，则是错误的。而非const对象可调用非const成员函数与const成员函数。

{:.info}

注：如果两个成员函数只是常量性不同，可以被重载。const只能作用于成员函数，不能作用于全局函数。

#### 4.4.2 const 数据成员

常量数据成员**必须**在构造函数的成员初始化列表中进行初始化。当常量整型数据成员同时被声明为static时，可使用外部初始化。

**C++中static、const以及static const成员变量的初始化：**

1. static静态成员变量不能在类内初始化，在类的内部只是声明，定义必须在**类定义体外部**，通常在类的实现文件中初始化，static关键字只能用于类定义体内部的声明中，定义时不能标示为static。

   ```c
   class Test{
       static int A;
   };
   int Test::A = 1;
   ```

   

2. const现在可以在**类定义**处初始化。const数据成员只在某个对象生存期内是常量，而对于整个类而言却是可变的。因为类可以创建多个对象，不同的对象其const数据成员的值可以不同。所以不能在类的声明中初始化const数据成员，因为类的对象没被创建时，编译器不知道const数据成员的值是什么。

3. 要想建立在整个类中都恒定的常量，应该用类中的**枚举常量**来实现，或者**static const**。

```c
class Test{
public:
    Test() : a(0) {}
    enum { size1 = 100, size2 = 200};
private:
    const int a;
    static int b;
    const static int c;
};

int Test::b = 0;
const int Test::c = 0;
```



## 5 内存管理与释放

C/C++程序中使用的内存主要分为以下几个部分：栈区、堆区、全局（静态）存储区、文字常量区、代码区（段）。

**堆和栈的区别**：

- 栈区（stack），由编译器自动分配释放，存放函数的参数值，局部变量的值等，其操作方式类似于数据结构中的栈，速度较快。
- 堆区（heap），一般由程序员分配内存释放，若程序员不释放，程序结束时由操作系统回收。分配方式类似于链表。一般速度较慢，而且容易产生内存碎片，不过用起来方便。

### 5.1 C语言内存操作函数

每个程序在执行时都占用一块可用的内存空间，用于存放动态分配的对象，此内存空间称为**程序的自由存储区或堆**。C语言程序使用一对标准库函数malloc/free在自由存储区中分配存储空间。

### 5.2 C++内存管理

C++使用new/delete来实现对内存的分配和释放。

#### 5.2.1 动态创建对象的初始化

通常，动态创建对象如果不提供显式初始化，那么对于类类型的对象，用该类的默认构造函数初始化；而内置类型的对象则无初始化。

```c
string *ps=new string;//调用默认构造函数初始化
int *pi=new int;//无初始化
```

显式初始化：

```c
string *ps=new string();//调用默认构造函数初始化
int *pi=new int();//pi指向一个初始化为0的int值
```

因此对于提供了默认构造函数的类类型，没有必要对其对象进行显式初始化；而对于内置类型或没有定义默认构造函数的类型，采用不同的初始化方式则有显著的区别（一个无初始化，一个则被初始化为0）。

动态创建的对象用完后，程序员**必须**显式地将该对象占用的内存释放给自由存储区，否则会出现内存泄漏。

在回收用new分配的单个对象的内存空间时用delete，回收用new[]分配的一组对象的内存空间时用delete[]。

#### 5.2.2 const对象的动态分配与回收

C++允许动态创建const对象：

```c
const int *pci=new const int(1024);
```

动态创建的const对象必须在创建时初始化，并且一经初始化，其值就不能再修改。

删除const对象仍使用：`delete pci;`。

#### 5.2.3 new和delete的执行过程

- **new的执行过程**：首先，调用名为operator new的标准库函数，**分配足够大的原始的未类型化的内存**，以保存指定类型的一个对象；接下来，**运行该类型的一个构造函数**，用指定初始化构造对象；最后，**返回指向新分配并构造的对象的指针**。

- **delete的执行过程**：首先，对指针指向的对象**运行适当的析构函数**；然后，通过调用名为operator delete的标准库函数**释放该对象所有内存**。

#### 5.2.4 malloc/free与new/delete的异同

相同点：都可用于申请动态内存和释放内存。

不同点：

1. malloc/free是C/C++语言的**标准库函数**，new/delete是C++的**运算符**；

   由于malloc/free不是运算符，不在编译器控制权限之内，所以**无法执行构造函数和析构函数**。

2. new自动计算需要分配的空间，而malloc需要手工计算字节数；

3. new是类型安全的，而malloc不是（因为new内置了sizeof、类型转换和类型安全检查功能），比如：

   ```c
   int* p=new float[2];//编译时指出错误
   int* p=(int*)malloc(2*sizeof(double));//编译时无法指出错误
   ```

4. new调用operator new分配足够的空间，并调用相关对象的构造函数，而malloc不能调用构造函数；delete将调用该实例的析构函数，然后调用类的operator delete以释放该实例占用的空间，而free不能调用析构函数；

   {:.info}

   注：如果对象有多个构造函数，那么new的语句也可以有多种形式；而如果new创建对象数据，那么只能使用对象的无参数构造函数。

5. malloc/free需要库文件支持，new/delete则不需要。

### 5.3 如何减少频繁分配内存（new/malloc）造成的内存碎片

使用**内存池**，这是一种分配方式。通常直接使用new/malloc等申请内存，这样做的缺点在于：由于所申请的内存块大小不定，当频繁使用时会造成大量的内存碎片进而降低性能。内存池则是在真正使用内存之前，先申请一定数量的、大小相等（一般情况下）的内存块留作备用。当有新的内存需求时，就从内存池中分出一部分内存块，若内存块不够则再继续申请新的内存。这样做的一个显著优点就是尽量避免了内存碎片，使得内存分配效率得到提升。

### 5.4 内存泄漏

常说的内存泄漏指堆内存的泄露，堆内存是指程序从堆中分配的，大小任意的内存块，使用完后必须显式释放的内存。应用程序一般使用malloc/realloc/new等函数从堆中分配到一块内存，使用完后，程序必须负责调用相应的free/delete释放该内存块，否则，这块内存就不能被再次使用，也就发生了内存泄漏。

## 6 生命周期和作用域

生命周期：是一个变量存在的周期。

作用域：一个变量可以被引用的范围，如：全局作用域、文件作用域、局部作用域。可以用{}、static修饰符等来指定一个变量的作用域和生命周期。

|     Name     |   作用域   |           生命周期           |                           引用方法                           | 内存分布           | 注                                                           |
| :----------: | :--------: | :--------------------------: | :----------------------------------------------------------: | ------------------ | ------------------------------------------------------------ |
|   全局变量   | 全局作用域 |      程序运行期一直存在      | 其他文件中要使用必须用extern关键字声明要引用的全局变量或通过包含头文件的方式 | 全局（静态）存储区 | 如果两个文件都定义了相同名字的全局变量，则连接出错：变量重定义 |
| 全局静态变量 | 文件作用域 |      程序运行期一直存在      |                  static关键字，const关键字                   | 全局（静态）存储区 | 只要文件不互相包含，在两个不同的文件中是可以定义完全相同的两个全局静态变量的，他们是两个完全不同的变量 |
| 静态局部变量 | 局部作用域 |      程序运行期一直存在      |                   局部作用域中用static定义                   | 全局（静态）存储区 | 只被初始化一次，多线程须加锁保护                             |
|   局部变量   | 局部作用域 | 程序运行出局部作用域即被销毁 |        局部作用域用auto指示符定义，也可省略auto指示符        | 栈区               |                                                              |

## 7 缓冲区溢出
缓冲区溢出是指向缓冲区内填充数据位数超过了缓冲区本身的容量限制，导致**溢出的数据覆盖在合法数据上**的情况。理想的情况是程序检查数据长度并不允许超过缓冲区长度的字符，但是绝大多数程序都会假设数据长度总是与所分配的内存空间相匹配，这就为缓冲区溢出埋下隐患。

造成缓冲区溢出的原因是程序中没有仔细检查用户输入的参数。

```c
void func(char* str)
{
    char buff[16];
    strcpy(buff,str);
}//只要str的长度超过16，就会造成buff溢出，程序运行出错
```