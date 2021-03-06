---
title: 【C++笔记】08 指针与引用
tags: C++笔记
layout: article
key: CPP_NOTES_08
mode: immersive
header:
  theme: dark
article_header:
  type: cover
  image:
    src: assets/images/cover2.jpg
---



## 1 指针

### 1.1 指针的声明

```c
string *ps1;
```

一个有效的指针必然是以下三种状态之一：

1. 保存一个特定对象的地址；
2. 指向某个对象后面的另一个对象；
3. 0值。

若指针保存0值，表明它不指向任何对象。未初始化的指针是无效的，直到给该指针复制后，才可使用它。

### 1.2 typedef

C语言允许使用typedef说明一种新类型名，来代替已有类型名，形式为：

```c
typedef 类型名 标识符;
```

其中类型名是在此语句前已有的类型标识符，标识符是用作新类型名的用户自定义标识符。例如：

```c
typedef float REAL;
typedef char* PCHAR;
REAL a,b; //等价于float a,b;
PCHAR p; //等价于char* p；
```

即typedef的作用仅仅是给已存在的类型名起一个“别名”，且原有类型名依然有效。

**`typedef char* String_t;`和`#define String_d char*;`在使用上的区别**：

前者声明是一个类型的别名，在**编译时**处理，**有类型检查**；后者是一个简单的替换，在**预编译时**处理**无类型检查**。从使用上来说，`String_t a,b;`中的a，b都是char* 类型的，但`String_d a,b;`中只有a是char* 类型的，b是char型的。

typedef的一种**特殊情况**是：

```c
typedef string* pstring;
const pstring cstr;
```

这里的cstr其实是一个指向string类型对象的const指针，该定义等价于：

```c
string* const cstr;
```

### 1.3 void* 指针

C++提供了一种特殊的指针类型void* ，可以保存任何类型对象的地址。

```c
double obj=3.14;
double *pd=&obj;
void *pv=&obj;
pv=pd;
```

void* 表明该指针与一地址值相关，但不清楚存储在此地址上的对象的类型。**void* 指针只支持几种有限的操作**：

1. 与另一个指针进行比较；
2. 向函数传递void* 指针或从函数返回void* 指针；
3. 给另一个void* 指针赋值。

**不允许使用void* 指针操纵它所指的对象**。

{:.info}

注：当函数返回void* 类型时表示返回一个特殊的指针类型，而不是像函数返回类型为void那样表示无返回值。

### 1.4 指向指针的指针

指针本身也是可以用指针指向的内存对象。指针占用内存空间存放其值（值为一个地址），因此指针的存储地址也可存放在指针中。

```c
int ival=1024;
int *pi=&ival;
int **ppi=&pi;
```

### 1.5 函数指针

函数指针是指指向函数而非指向对象的指针。像其他指针一样，函数指针也指向某个特定的函数类型，函数类型由其**返回类型**以及**形参表**确定，而与函数名无关：

```c
bool (*pf)(const string&, const string&);
```

此语句将pf声明为指向函数的指针，它所指向的函数带有两个`const string&`类型的形参和bool类型的返回值。

{:.info}

注：*pf两侧的圆括号是必需的。

#### 1.5.1 用typedef简化函数指针的定义

函数指针类型相当冗长，可以使用typedef为指针类型定义同义词，简化使用：

```c
typedef bool (*cmpFcn)(const string&, const string&);
```

该定义表示cmpFcn是一种指向函数的指针类型的名字，该指针类型为“指向返回bool类型并带有两个const string引用形参的函数的或指针”。在使用时，只需直接使用cmpFcn即可。如:`cmpFcn pf1=0;`

在引用函数名但又没有调用该函数时，函数名将被自动解释为指向函数的指针。假设有函数：

```c
bool lengthCompare(const string&, const string&);
```

除了用作函数调用的左操作数外，对lengthCompare的任何使用都被解释为如下类型的指针：

```c
bool (*)(const string&, const string&);
```

因此可以使用函数名对函数指针做初始化或赋值：

```c
cmpFcn pf1=0;//ok
cmpFcn pf2=lengthCompare;//ok
pf1=lengthCompare;//ok
pf2=pf1;//ok
```

此时，直接引用函数名等效于在函数名上应用取地址操作符：

```c
cmpFcn pf1=lengthCompare;
cmpFcn pf1=&lengthCompare;
```

函数指针只能通过**同类型**的**函数**或**函数指针**或**0值常量表达式**进行初始化或赋值。将函数指针初始化为0，表示该指针不指向任何函数。指向不同函数类型的指针之间不存在转换。

#### 1.5.2 函数指针的使用

函数指针可以用于调用它所指向的函数。可以**不需要使用解引用操作符**，直接通过指针调用函数。

```c
typedef bool (*cmpFcn)(const string&, const string&);
bool lengthCompare(const string&, const string&);

cmpFcn pf1=lengthCompare;
lengthCompare("h1", "bye");//直接调用lengthCompare
pf1("h1", "bye");//利用函数指针调用，未使用*
(*pf1)("h1", "bye");//利用函数指针调用，使用*
```

{:.info}

注：如果指向函数的指针没有初始化，或者具有0值，则该指针不能在函数调用中使用。只有当指针已经初始化，或被赋值为指向某个函数，才能用来调用函数。

#### 1.5.3 函数指针形参

函数的形参可以是指向函数的指针：

```c
void useBigger(const string&, const string&, bool(const string&, const string&));
//等价于
void useBigger(const string&, const string&, bool(*)(const string&, const string&));
```

#### 1.5.4 返回指向函数的指针

函数可以返回指向函数的指针：

```c
int (*ff(int))(int*, int);
```

首先观察`ff(int)`，ff为一个带有`int`形参的函数，该函数返回`int (*)(int*, int);`这是一个指向函数的指针，所指向的函数返回`int`型并带有两个分别是`int*` 和`int`型的形参。

可以用typedef来使之易于理解：

```c
typedef int(*PF)(int*, int);
PF ff(int);
```

允许将形参定义为函数类型，但函数的返回类型则**必须是指向函数的指针**，而不能是函数。具有函数类型的形参所对应实参将被自动转换为指向相应函数类型的指针。但当返回的是函数时，则无法实现：

```c
typedef int func(int*, int);//func是一个函数，而不是一个函数指针
void f1(func);//正确，f1的形参是一个函数指针，func自动转换为函数指针
func f2(int);//错误，func无法被自动转换
func* f3(int);//正确，f3返回一个函数指针
```

#### 1.5.5 函数指针数组

```c
int (*a[10])(int);
```

表示一个有10个指针的数组，每个指针指向一个函数，该函数有一个整型参数并返回一个整型。

#### 1.5.6 指向重载函数的指针

C++允许使用函数指针指向重载的函数：

```c
extern void ff(vector<double>);
extern void ff(unsigned int);

void (*pf1)(unsigned int) = &ff;//正确，pf1指向参数为unsigned int版本的函数ff
void (*pf2)(int) = &ff;//错误，参数不匹配
double (*pf3)(vector<double>);
pf3=&ff;//错误，返回值不匹配
```

指针的类型必须与重载函数的一个版本精确匹配。如果没有精确匹配的函数，则对该指针的初始化或赋值都将导致编译错误。

### 1.6 野指针

野指针是指向不可用内存的指针。

造成野指针的**原因**：

1. 任何指针变量在被创建时，不会自动成为NULL指针，其默认值是随机的，此时的指针就是野指针。
2. 当指针调用free或delete释放后，未能将其设置为NULL，也会导致该指针变为野指针，此时虽然free/delete把指针所指向的内存释放了，但并没有将指针本身释放掉。
3. 指针操作超越了变量的作用范围。



## 2 引用

引用，是一个特殊的变量，这个变量的内容是绑定在这个引用上面的对象的地址，而使用这个变量时，系统就会自动根据这个地址去找到它绑定的变量，然后再对变量进行操作。本质上说，引用其实还是指针，只不过这个指针是不能修改的，任何对它的操作都会发生在这个指针所指向的地方，而不是在这个指针身上。

{:.info}

注：C++中规定一旦定义了引用，就必须把它跟一个变量绑定起来，并且不能修改这个绑定。

不能定义引用类型的引用（即没有对引用的引用），但可以定义任何其它类型的引用。

数组可以有引用，但没有引用数组。

**引用和指针的几个重要区别：**

1. 引用不能为空，**在被创建时必须初始化**。而指针可以是空值，可以在任何时候初始化。
2. 一旦一个引用被初始化为指向一个对象。就不能被改变为另一个对象的引用。而指针则可以在任何时候指向另一个对象。
3. 不可能有NULL引用，必须确保引用是和一块合法的存储单元关联。
4. `sizeof(引用)`得到的是所指向的变量（对象）的大小。而`sizeof(指针)`得到的是指针本身的大小。
5. 给引用赋值修改的是该引用所关联的对象的值，而不是使引用与另一个对象关联。
6. 引用使用时不需要解引用。而指针需要解引用。引用和指针的自增（++）操作运算意义不一样。
7. 如果返回动态分配的对象或内存，必须使用指针，引用肯能会引起内存泄漏。
8. 当使用&运算符取一个引用的地址时，其值为所引用变量的地址；而对指针使用&运算符，取的是指针变量的地址。

### 2.1 const 引用

const引用时指向const对象的引用，当引用的对象是const对象时，引用也必是const：

```c
const int ival=1024;
const int &refval=ival;//正确
int &ref2=ival;//错误
```

如果既要利用引用提高程序的效率，又要保护传递给函数的数据不在函数中被改变，就应使用const引用。const引用主要用于定义一个普通变量的只读属性的别名，作为函数的传入形参，避免实参再调用函数中被意外改变。

### 2.2 引用做类的数据成员

引用可以作为类的数据成员。

引用类型数据成员的初始化有以下**特点**：

1. 不能直接在构造函数里初始化，**必须用到初始化列表**。
2. 凡是有引用类型的数据成员的类，必须定义构造函数。

```c
class Test
{
public:
    Test(int ii):i(ii),ci(i),ri(ii){}//ci与ri必须在成员初始化列表中初始化，因此必须自定义构造函数，书写成员初始化列表
private:
    int i;
    const int ci;
    int &ri;
};
```