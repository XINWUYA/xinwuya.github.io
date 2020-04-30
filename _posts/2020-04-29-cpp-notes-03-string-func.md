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

## strlen(s)

说明: 返回s的长度，不包括字符串结束符 null

	size_t strlen(const char* str)
    {
    	_ASSERT(str != nullptr);
    	size_t ret = 0;
    	while((*str++)!= '\0')
    		ret++;
    	return ret;
    }
    
    size_t strlen(const char* str)
    {
    	_ASSERT(str != nullptr);
    	return *str == '\0' ? 0 : (1 + strlen(++str));
    }

注：若有

    char ca1[]={'C','+','+'};//末尾没有null字符
    char ca2[]={'C','+','+','\0'};//末尾显式添加null字符

strlen函数直到遇到ca1数组末尾后的内存单元刚好有'\0'字符时，才停止统计字符数，故strlen(ca1)的值未定义，strlen(ca2)=3。

## strcmp(s1, s2)

说明: 比较两个字符串s1和s2是否相同。若s1与s2相等，返回0；若s1大于s2，返回正数；若s1小于s2，则返回负数【两个字符串自左向右逐个字符按ASCII码相比较，直至出现不同的字符或遇'\0'为止】

	int strcmp(const char* str1, const char* str2)
    {
    	_ASSERT(str1 != nullptr && str2 != nullptr);
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

注：之所以转成unsigned char* 型，是因为如果不转成unsigned char* 型的话，对于超过127的字符，会按照对应的二进制补码转换成对应的负数，从而导致错误的结果。

## strcat(s1, s2)

说明: 将s2连接到s1后，并返回s1【覆盖s1结尾处的'\0'并在新的结尾添加'\0'】

	char* strcat(char* str1, const char* str2)
    {
    	_ASSERT(str1 != nullptr && str2 != nullptr);
    	char * ret = str1;
    	while (*str1)
    		str1++;
    	while (*str1++ = *str2++);
    	return ret;
    }

## strcpy(s1,s2)

说明: 将s2复制给s1，并返回s1【把从s2地址开始且含有null结束符的字符串复制到以s1开始的地址空间】

	char* strcpy(char* str1, const char* str2)
    {
    	_ASSERT(str1 != nullptr && str2 != nullptr);
    	char * ret = str1;
    	while ((*ret++ = *str2++) != '\0');
    	return ret;
    }

## strncat(s1,s2,n)

说明: 将s2的前n个字符连接到s1后面，并返回s1

## strncpy(s1,s2,n)

说明: 将s2的前n个字符复制给s1，并返回s1