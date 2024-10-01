<!-- GFM-TOC -->
- [关于编译](#关于编译)
- [gcc参数](#gcc参数)
- [objdump](#objdump)
- [学习资料](#学习资料)
<!-- GFM-TOC -->
---

## 关于编译
1. 预处理 -> 编译 -> 汇编 -> 链接
- 预处理：生成.i文件            
- 编译：.i文件 -> 汇编代码(.s文件)             
- 汇编：汇编代码 -> 二进制代码目标文件(.o文件)
- 链接：多个.o文件组合合并成一个可执行二进制代码（.c -> .o -> .out，即linux下的可执行文件）
  
2. 处理大型工程时将面对以下问题：
- 手动地一一编译实在太麻烦，太浪费精力；
- 这些源文件的编译有顺序要求，为了满足此依赖关系需要设计一个流程；
- 编译整个项目需要难以忍受的大量时间，应当考虑到一部分未更改的源文件不需要重新编译。

## gcc参数
- gcc one.c two.c three.c -o xxx
  - 默认情况输入源文件将产生**可执行文件**
- gcc one.o two.o three.o -o xxx
- gcc one.c two.c three.c -c 
  - 输入**目标文件**将产生**可执行文件**
- `-o`指定目标文件的名称
- `-c`将源文件编译成**目标文件**
- `-Idir`指定**头文件**的所在路径为`dir`
- `-g`产生**调试**信息
- `-O`在编译、链接过程中进行优化
- gcc -c `xxx.s` -o xxx(.out)
## objdump
- `objdump` -d xxx(.o/.out) >> xxx.txt


## 学习资料
1. [CSDN : gcc简介和命令行参数说明](https://blog.csdn.net/yueguangmuyu/article/details/116703618)
2. [如何将汇编语言转化为hex机器码](https://blog.csdn.net/fjh1997/article/details/105334158)
 