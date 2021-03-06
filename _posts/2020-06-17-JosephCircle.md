---
layout: post
title:  "算法：约瑟夫环问题"
date:   2020-06-17 09:44:01 +0800
categories: 算法
tag: 算法


---


* content
{:toc}


## 题目描述：

0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

**示例1：**

```
输入：n = 5， m = 3

输出： 3
```

**示例2：**

```
输入：n = 10，m = 17

输出：2
```

**限制：**

- 1 <= n <= 10^5
- 1 <= m <= 10^6

## 题解：

1. **暴力解法：**

   把题述圆圈看作循环数组，每次数到第m个将其设为已删除节点标志，从下一个节点重新开始数第m个，....，直到剩最后一个数字。

2. **数学方法：**

   主要思路：构建删除第m个节点前和删除后，两个数组节点的index对应关系。

   ------

   

如图，是一个输入为n = 5， m = 3的示例：

![/post_images/2020-06-17-JosephCircle/JsoephCircle-img.png]({{ '/post_images/2020-06-17-JosephCircle/JsoephCircle-img.png' | prepend: site.baseurl  }})

![/post_images/2020-06-17-JosephCircle/JsoephCircle-img2.png]({{ '/post_images/2020-06-17-JosephCircle/JsoephCircle-img2.png' | prepend: site.baseurl  }})

**核心思路：**

1. ***每次删除第m个节点前后的数组，其index之间是存在关联的：***每次删除第m个节点后，从下一个节点开始重新编号0，1，2,...。
2. ***最终都是最后剩下的1个数字，剩下的最后的那个数字（即最终答案，设为res）在最后数组中的index为0，这是必然的。***如上图第5个数组。

因此：我们可以根据最后一个数组中res的index为0，根据删除m前后index之间的关系，反推回数组长度为n时res在原数组的index，即为最终答案。

------



**分析：**

如上图前2个数组所示，以下分析请对照上图看：

我们标记：第1个数组为old数组（删除m前），第2个数组为new数组（删除m后）。则需归纳出old数组中节点的index（设为old）和new数组中节点index（设为new）的对应关系。

------

**符号说明：**

**len：**new数组大小

**len + 1：**old数组大小

**m：**题中给出的删掉第m个数

**new：**new数组各节点的index

**old：**old的数组对应节点的index

------

**递推公式：**

删除的第m个节点的下一个节点重新从0开始编号：说明m节点在old数组中的index为m - 1（如果从1开始编号，则为m）；下一个节点在old数组中index为m，在new数组中为0；再下一个节点在old数组中index为m + 1，在new数组中为1.......

那么反过来想，在new数组中的0节点，在old数组中的编号就是new + m，即`old = new + m`；

但是，对new数组中的2节点和3节点，如果按照old = new + m的计算方式，其分别对应old数组中的5节点和6节点，已经超过了old数组的长度，相当于多算了old数组长度的部分，即len + 1，因此，这样的情况通过取余运算即可（因为是多算了所以是取余，java中的%运算符为取余运算），即`old = (new + m) % (len + 1)`

------



## **代码：**

```
class Solution {
    public int lastRemaining(int n, int m) {
        int old = 0;
        for(int i = 1; i < n; i++){
            old = (old + m) % (i + 1);
        }
        return old;
    }
}
```

**代码说明：**

1. old初始化为0，利用java赋值特性，相当于new初始化为0；
2. for循环n-1次，从数组长度为1补齐到n，共需通过`old = (new + m) % (len + 1)`计算n-1次；
3. 代码中`old = (old + m) % (i + 1);`左边的old是上述分析中的old，右边的old相当于上述分析中的new；
4. 计算n-1次，所得的old即为最后剩下的数在数组长度为n的数组中的index。