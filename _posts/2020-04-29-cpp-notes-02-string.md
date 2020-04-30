---
title: 【C++笔记】02 字符串
tags: C++笔记
layout: article
key: CPP_NOTES_02
mode: immersive
header:
  theme: dark
article_header:
  type: cover
  image:
    src: assets/images/cover2.jpg
---



## 1 字符串与子串、子序列

**字符串**：由**零个**或**多个**字符组成的有限串行。

**子串**：串中任意个**连续的字符**组成的子序列，**空串**是任意串的子串。

**子序列**：**不要求字符连续**但顺序与其所在主串中相一致，如：`"abcd"`和`"ad"`最长的公共子序列为`"ad"`。



## 2 标准库提供的字符串处理函数

### 2.1 字符串处理函数

|        函数        |                             说明                             |
| :----------------: | :----------------------------------------------------------: |
|    `strlen(s)`     |           返回`s`的长度，不包括字符串结束符`null`            |
|  `strcmp(s1, s2)`  | 比较两个字符串`s1`和`s2`是否相同。若`s1`与`s2`相等，返回0；若`s1`大于`s2`，返回正数；若`s1`小于`s2`，则返回负数【两个字符串自左向右逐个字符按ASCII码相比较，直至出现不同的字符或遇`'\0'`为止】 |
|  `strcat(s1,s2)`   | 将`s2`连接到`s1`后，并返回`s1`【覆盖`s1`结尾处的`'\0'`并在新的结尾添加`'\0'`】 |
|  `strcpy(s1,s2)`   | 将`s2`复制给`s1`，并返回`s1`【把从`s2`地址开始且含有`null`结束符的字符串复制到以`s1`开始的地址空间】 |
| `strncat(s1,s2,n)` |        将`s2`的前`n`个字符连接到`s1`后面，并返回`s1`         |
| `strncpy(s1,s2,n)` |          将`s2`的前`n`个字符复制给`s1`，并返回`s1`           |

> 注：这些函数假设他们所修改的字符串**具有足够大的空间**接收本函数新生成的字符，程序员必须确保目标字符串足够大。

### 2.2 memcpy

功能：从源src所指的内存地址的起始位置开始，拷贝n个字节到目标dest所指的内存地址的起始位置，返回指向dest的指针。

```c
void* memcpy(void* dest, const void* src, size_t n);
```

**`strcpy`与`memcpy`的区别**：

- 复制的内容不同。`strcpy`只用于字符串复制，除了复制字符串内容，还会复制字符串的结束符；`memcpy`可以复制任意内容，例如字符数组、整型、结构体、类等，对于需要复制的内容没有限制，用途更广。
- 复制的方法不同。`strcpy`不需要指定长度，遇到被复制字符的'\0'时结束，容易溢出；`memcpy`对需要复制的内容没有限制，因此用途更广。
- 用途不同。`strcpy`通常用于复制字符串；`memcpy`则用于复制其他类型数据。

### 2.3 memset
功能：将s中的前n个字节用ch替换并返回s，作用是在一段内存块中填充某个给定的值，是对较大结构体或数组进行清零操作的一种最快方法。

```c
void* memset(void* s, int ch, size_t n);
```



## 3 字符串包含问题

### 3.1 串的模式匹配算法

如主串`A="ababcabcacbab"`，子串为`B="abcac"`。

- **方法一：暴力求解**

  **BF算法**：时间复杂度为`O((m-n+1)*n)`

  ```c
  const char* findSubstring_BF(const char *str, const char *substr)
  {
  	assert(str != nullptr && substr != nullptr);
      
  	int m = strlen(str);
  	int n = strlen(substr);
  	if (m < n)
  		return nullptr;
  
  	for(int i =0; i < m - n; ++i)
  	{
  		for (int k = 0; k < n; ++k)
  			if (str[i + k] != substr[k])
  				break;
  		if (i == n)
  			return str+i;
  	}
  
  	return nullptr;
  }
  ```

  **显式回退**：

  ```c
  const char* findSubstring_EF(const char *str, const char *substr)
  {
  	assert(str != nullptr && substr != nullptr);
  
  	int m = strlen(str);
  	int n = strlen(substr);
  	int i = 0, k = 0;
  	for(;i < m && k < n; i++)
  	{
  		if (str[i] == substr[k]) k++;
  		else
  		{
  			i -= k;//显式回退
  			k = 0;
  		}
  	}
  	if (k == n) return str + i - n;
  	else return nullptr;
  }
  ```

- **方法二：KMP算法**：时间复杂度为`O(m+n)`

  KMP算法的比较过程是：

  第一次匹配：

  |  a   |  b   |  a   |  b   |  c   |  a   |  b   |  c   |  a   |  c   |  b   |  a   |  b   |
  | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
  |  a   |  b   |  c   |      |      |      |      |      |      |      |      |      |      |

  第二次匹配：直接拿模式串的第1位与主串的第3位（此位上一次匹配失败）作比较。

  |  a   |  b   |  a   |  b   |  c   |  a   |  b   |  c   |  a   |  c   |  b   |  a   |  b   |
  | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
  |      |      |  a   |  b   |  c   |  a   |  c   |      |      |      |      |      |      |

  第三次匹配：直接拿模式串的第2位与主串的第7位作比较。此时模式串的第1位没有参与比较，是因为KMP算法已经记录了此位相等。

  |  a   |  b   |  a   |  b   |  c   |  a   |  b   |  c   |  a   |  c   |  b   |  a   |  b   |
  | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
  |      |      |      |      |      |      |  b   |  c   |  a   |  c   |      |      |      |

  查找模式串成功。

  **KMP算法的基本思想**为：在每次匹配过程中出现字符比较不等时，不需要回溯主串，而是利用已经得到的“部分模式”结果将模式串**向右“滑动”尽可能远的一段距离**后，继续进行比较，且此时并不一定是拿模式串的第一位继续比较。

  下面给出KMP算法的代码：

  ```c
  const char* findSubstring_KMP(const char* src, int slen, const char* patn, int plen, const int* nextval, int pos)
  {
  	int i = pos, j = 0;
      while(i < slen && j < plen)
      {
          if(j == -1 || src[i] == patn[j])
          {
              ++i;
              ++j;
          }
          else
              j = nextval[j];//当匹配失败时，直接用p[j_next]与s[i]比较
      }
      
      if(j >= plen)
          return i - plen;
      else
          return -1;
  }
  ```

  其中`nextval`数组可由以下方式计算：

  ```c
  void get_nextval(char const* ptrn, int plen, int* nextval)
  {
      int i = 0, j = -1;
      nextval[i] = -1;
      while(i < plen - 1)
      {
          if(j == -1 || ptrn[i] == ptrn[j])
          {
              ++i;
              ++j;
              if(ptrn[i] != ptrn[j])
              	nextval[i] = j;
              else
              	nextval[i] = nextval[j];
          }
          else
              j = nextval[j];
      }
  }
  ```

### 3.2 字符串移位包含问题

判断`s2`是否能被`s1`做循环位移得到的字符串包含，可以转化为判断`s2`是否被`s1s1`包含，在写代码时，则可以通过取模操作。

```c
bool find_CyclicDisplacement(const char* str, const char* substr)
{
	assert(str != nullptr && substr != nullptr);
    
	int m = strlen(str);
	int n = strlen(substr);
	if (m < n) 
        return false;

	int p;
	for(int i =0; i < m; ++i)
	{
		p = i;
		for(int k = 0; k < n; ++k)
		{
			if (str[p++%m] != substr[k]) 
                break;
		}
		if (p - i == n)
			return true;
	}
	return false;
}
```


### 3.3 字符串转换为数字

```c
int convertStrToInt(const char* str)
{
	long long result = 0;

	if(str != nullptr)
	{
		const char* tempStr = str;
		bool isNegative = false;
		if (*tempStr == '+')
			tempStr++;
		else if(*tempStr == '-')
		{
			tempStr++;
			isNegative = true;
		}
		while(*tempStr != '\0')
		{
			if(*tempStr >= '0' && *tempStr <= '9')
			{
				result = result * 10 + (*tempStr - '0');
				if(result > std::numeric_limits<int>::max())
				{
					result = 0;
					break;
				}
				tempStr++;
			}
			else
			{
				std::cout << "Contains non-number members." << std::endl;
				result = 0;
				break;
			}
		}
		if(*tempStr == '\0')
		{
			if (isNegative)
				result = -result;
		}
	}
	return result;
}
```

### 3.4 判断字符串中所有字符是否都不相同

通用解决方法如下：时间复杂度为`O(n)`，n为字符串长度

```c
bool char_set[256];
int isUniqueCharString(const char* str)
{
	assert(str != nullptr);
    
	int len = strlen(str);
	for(int i = 0; i < len; ++i)
	{
        int val = str[i];
		if(char_set[val]) 
            return 0;
		char_set[val] = true;
	}
	return 1;
}
```

如果要求在上述算法的基础上进一步降低空间要求，可以使用bit-map，如果假设字符串仅包含从a到z的字符，则可以将空间需求降低到一个int。

```c
int isUniqueCharString(const char* str)
{
	assert(str != nullptr);
    
    int checker = 0;
	int len = strlen(str);
	for(int i = 0; i < len; ++i)
	{
        int val = str[i] - 'a';
		if((checker & (1 << val)) > 0) 
            return 0;
		checker |= (1 << val);
	}
	return 1;
}
```

如果可以改变原始字符串，也可先排序（时间复杂度为`O(nlogn)`），然后对比相邻字符是否存在相同的情况。

## 4 snprintf

```c
int snprintf(char* str, size_t size, const char* format, ...);
```

其功能为将可变个参数(...)按照`format`格式转化为字符串，然后将其复制到`str`中。

- 如果格式化后的字符串长度小于size，则将此字符串全部复制到`str`中，并给其后添加一个`'\0'`；
- 如果格式化后的字符串长度大于size，则只将其中的size-1个字符复制到`str`中，并给其后添加一个`'\0'`。

例如：`int cx = snprintf ( buffer, 100, "The half of %d is %d", 60, 60/2 );`

其结果：`The half of 60 is 30`