---
layout: post
title:  "C语言漫谈（一）——C和指针"
modified:   2014-02-14 19:47:02 +0800
categories: C
tags: [基础]
---

> 现在很多高级语言的程序员回过头来学 C。C 的精华就在于指针。为了能去北京工作，我该补课了！

> 本文基本都是《Pointers on C》书中的知识，由于时间和篇幅原因，不能一次性都写出来。我会尽量把学会的知识点无序的展示出来，仅用于个人记录。

## Bubble Sort

> 学 C 语言每个人都会学到一个最简单的排序方法：***冒泡排序***

> 冒泡排序有很多种方式——异或门操作符的方式是对指针的一种基础的诠释。

> 异或 (exclusive or) ：相同为0，相异为1。在 C 语言中操作符为 `^`。

> 举个栗子：    
`1010 1101 ^ 1001 0010` 的结果是 `0011 1111`。    
换算成十进制：`173 ^ 146 == 63`

**XOR Gate:**

```c
int main (int argc, char * argv[]) {

  int array[] = {2, 5, 3, 1, 6, 4, 7};

  int count = sizeof(array) / sizeof(int);

  for (int pointer = 0; pointer < count - 1; pointer++) {
      for (int loop = 0; loop < count - pointer - 1; loop++) {

          int current = array[loop];
          int next = array[loop + 1];

          if (current > next) {
              // 这里我们只用第一次运算来做讲解
              // current = 2, next = 5;
              // current = 0010, next = 0101;
              current = current ^ next;
              // current = 0010 ^ 0101;(2 ^ 5)
              // current = 0111;(7)
              next = current ^ next;
              // next = 0111 ^ 0101;(7 ^ 5)
              // next = 0010;(2)
              current = current ^ next;
              // current = 0111 ^ 0010;(7 ^ 2)
              // current = 0101;(5)
              // 至此，两个数字实现了位置交换。
          }

      }
  }

  for (int i = 0; i < count; i++) {
      // 输出一下结果验证一下：
      printf("array[%zi] == %zi\n",i ,array[i]);

  }

  return 0;
}
```

**Output:**

```c
array[0] == 1
array[1] == 2
array[2] == 3
array[3] == 4
array[4] == 5
array[5] == 6
array[6] == 7
```

## 面试时总会问到的几种指针

* `int ptr;`
* `int *ptr;`
* `int **ptr;`
* `int (*ptr)[];`
* `int *(*ptr)[];`

## 位运算

> C语言是为描述系统设计的，因此它应该具有汇编语言所以完成的一些功能。C语言既有高级语言的特点，又具有低级语言的功能。因而具有广泛的用途和很强的生命力。

|运算符|含义|
|:--:|:--:|
|`&`|按位与|
|`｜`|按位或|
|`^`|按位异或|
|`~`|取反|
|`<<`|左移|
|`>>`|右移|

> 由于markdown的表格原因，`|`无法正常显示，只好用中文的`｜`来代替。    

> **注意：**    
1. 位运算符中除 `~` 外，均为二目运算符，即要求两侧各有一个运算量。
2. 运算量只能是整型 `int` 或字符型 `char` 的数据，不能为实型数据 `float` or `long` or `double` etc.。(指数型 `2.5E3` 也属于实型数据。)


[To be continnued...]

