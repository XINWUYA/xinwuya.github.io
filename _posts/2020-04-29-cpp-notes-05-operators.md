---
title: 【C++笔记】05 运算符及其优先级
tags: C++笔记
layout: article
key: CPP_NOTES_05
mode: immersive
header:
  theme: dark
article_header:
  type: cover
  image:
    src: assets/images/cover2.jpg
---

## 1.运算符

1）赋值操作具有右结合特性，当表达式含有多个赋值操作时，从右向左结合。多个赋值操作中，各变量必须具有相同的数据类型，或者具有可转换为同一类型的数据类型。

2）前缀运算是“先变后用”，后缀运算是“先用后变”。

3）自增、自减运算符只作用于变量，而不能作用于常量或表达式。只要是标准类型的变量，不管是整型、实型，还是字符型，枚举型都可以作为运算对象。
如++6、(i+j)++、'A'++、++i+++j、(&p)++这5个表达式是不合法的。（其中++i+++j相当于++(i++)+j）

4）前置自增和后置自增有个重要的区别就是：前置自增能够用做左值表达式，后置自增只能用于右值表达式。这是因为，前置自增返回操作数本身，而后置自增返回一个临时变量。

5）++和--运算符的结合方向：自增、自减运算符及负号运算符的结合方向是从右向左。如k=-i++等效于k=-(i++)。

6）关系操作符（<、<=、>、>=）具有左结合的特性。

`if(i<j<k){ /***/ }`
只要k>1，上述表达式的值就为true。这是因为第二个小于操作符的左操作数是第一个小于操作符的结果：true/false。也就是该条件将k与0/1作比较。

7）逻辑与和逻辑或操作符总是先计算其左操作数，然后再计算其右操作数。只有在仅靠左操作数数的值无法确定该逻辑表达式的结果时，才会求解其右操作数。这种求值策略称为“短路求值”。

8）位操作符：

| 操作符 |   功能   |
| :----: | :------: |
|   &    |  按位与  |
|   \|   |  按位或  |
|   ^    | 按位异或 |
|   ~    |   取反   |
|   <<   |   左移   |
|   >>   |   右移   |


- 判断一个整数n是否位2的正整数次幂：

	`if(n>1 && ((n & (n-1)) == 0))`

- 两个相同的数异或后结果为0，且满足交换律。这一性质通过常用来寻找数成对出现时缺失的某一个数。

        //不使用第三方变量交换两个变量的值
        a=a^b;
        b=a^b;
        a=a^b;
        
        //不用算数运算符实现两个数的加法
        int add_no_arithm(int a, int b)
        {
        	if(b == 0) return a;
        	int sum = a ^ b;
        	int carry = a & b;
        	return add_no_arithm(sum, carry);
        }

9）移位运算符

<<和>>分别为左移和右移操作符，移位时的补位规则为：

|     类型      |  左移   |     右移     |
| :-----------: | :-----: | :----------: |
|      int      | 低位补0 | 高位补符号位 |
| unsigned  int | 低位补0 |   高位补0    |


注：负数左移时有可能会变成正数

## 2.返回n转化为二进制后包含1的数量：

- 方法一：

	    int fun(unsigned int n)
	    {
	    	int count = 0;
	    	while(n > 0)
	    	{
	    		n &= (n-1);
	    		count++;
	    	}
	    	return count; 
	    }
	    或
	    int fun(int n)
	    {
	    	int count = 0;
	    	while(n != 0)//考虑负数
	    	{
	    		n &= (n-1);
	    		count++;
	    	}
	    	return count; 
	    }

- 方法二：

		int fun(unsigned int n)
		{
			int count = 0;
			while(n)
			{
				if(n & 1)//判断最低位是不是为1
					count++;
				n >>= 1;//每次右移一位
			}
			return count; 
		}

- 方法三：

	    int fun(int n)
	    {
	    	int count = 0;
	    	unsigned int flag = 1;
	    	while(flag)
	    	{
	    		if(n & flag)
	    			count++;
	    		flag <<= 1;
	    	}
	    	return count; 
	    }

## 3.优先级：
~运算符 > 移位运算符 > 与、或、异或运算符。

## 4.类型自动转换
当同一个表达式中出现不同类型的量时，C++会根据不同的情况对操作数进行自动转换，分为“整型提升”和“运算时的转换”两种。

- 整型提升：在表达式计算中，C++会将bool、char、unsigned char、signed char、short和signed short型值都会自动转换成int型。（注：unsigned short 转int比较特殊：如果系统中int占4字节，unsigned short会转换成int，如果系统中int占2字节，则unsigned short会转换成unsigned int，避免了数据的丢失）
- 运算时的转换：当运算涉及两种类型时，较小的类型将会被转换成较大的类型，即表达力低的类型将会被转换成表达力高的类型。各类型表达能力由低到高为：int(等价于signed int)→unsigned int→long(等价于signed long)→unsigned long→float→double→long double

## 5.运算符优先级有几个简单的规则：

1）括号，下标，->和.(成员)最高；

2）单目的比双目的高；算术双目比其他双目的高；

3）移位运算高于关系运算；关系运算高于按位运算（与，或，异或）；按位运算高于逻辑运算；

4）三目的只有一个条件运算，低于逻辑运算。

5）赋值运算仅比“，”高，且所有的赋值运算符优先级相同，结合访问位从右向左。