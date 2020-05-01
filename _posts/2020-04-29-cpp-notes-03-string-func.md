---
title: 【C++笔记】03 字符串处理函数
tags: C++笔记
layout: article
key: CPP_NOTES_03
mode: immersive
header:
  theme: dark
article_header:
  type: cover
  image:
    src: assets/images/cover2.jpg
---



## 1 strlen(s)

功能：返回`s`的长度，不包括字符串结束符 `null`

```c
size_t strlen(const char* str)
{
    assert(str != nullptr);
    
    size_t ret = 0;
    while((*str++)!= '\0')
        ret++;
    return ret;
}

size_t strlen(const char* str)
{
    assert(str != nullptr);
    
    return *str == '\0' ? 0 : (1 + strlen(++str));
}
```

{:.info}

注：若有`char ca1[]={'C','+','+'};//末尾没有null字符`以及`char ca2[]={'C','+','+','\0'};//末尾显式添加null字符`两个字符数组，`strlen`函数直到遇到`ca1`数组末尾后的内存单元刚好有`'\0'`字符时，才停止统计字符数，故`strlen(ca1)`的值**未定义**，`strlen(ca2)=3`。



## 2 strcmp(s1, s2)

功能：比较两个字符串`s1`和`s2`是否相同。若`s1`与`s2`相等，返回0；若`s1`大于`s2`，返回正数；若`s1`小于`s2`，则返回负数。两个字符串自左向右逐个字符按ASCII码相比较，直至出现不同的字符或遇`'\0'`为止。

```c
int strcmp(const char* str1, const char* str2)
{
    assert(str1 != nullptr && str2 != nullptr);
    
    int ret = 0;
    while (!(ret = *(unsigned char*)str1 - *(unsigned char*)str2) && *str1)
    {
        str1++;
        str2++;
    }
    if (ret > 0) return 1;
    else if (ret < 0) return -1;
    return ret;
}
```

{:.info}

注：之所以转成`unsigned char*` 型，是因为如果不转成`unsigned char*` 型的话，对于**超过127的字符**，会按照对应的二进制补码转换成对应的**负数**，从而导致错误的结果。



## 3 strcat(s1, s2)

功能：将`s2`连接到`s1`后，并返回`s1`。覆盖`s1`结尾处的`'\0'`并在新的结尾添加`'\0'`。

```c
char* strcat(char* str1, const char* str2)
{
    assert(str1 != nullptr && str2 != nullptr);
    
    char * ret = str1;
    while (*str1)
        str1++;
    while (*str1++ = *str2++);
    return ret;
}
```



## 4 strcpy(s1,s2)

功能：将`s2`复制给`s1`，并返回`s1`。把从`s2`地址开始且含有`null`结束符的字符串复制到以`s1`开始的地址空间。

```c
char* strcpy(char* str1, const char* str2)
{
    assert(str1 != nullptr && str2 != nullptr);
    
    char * ret = str1;
    while ((*ret++ = *str2++) != '\0');
    return ret;
}
```



## 5 strncat(s1,s2,n)

功能：将`s2`的前`n`个字符连接到`s1`后面，并返回`s1`。



## 6 strncpy(s1,s2,n)

功能：将`s2`的前`n`个字符复制给`s1`，并返回`s1`。