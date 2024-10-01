## 一些概念
* RV内核上电后首先运行在M模式下。
* `OS运行在S模式下`；`用户程序运行在U模式下`。
* 优先级：M>S>U。
  * 内存管理与保护
    * 物理内存保护（PMP）
      * 在一些比较简单的处理器上实现（处理器只支持M模式和U模式）。
      * 由M模式指定U模式可以访问的内存地址。
    * 虚拟内存
      * 必须支持S模式和MMU。
      * 用于实现高级的操作系统（Unix-like）特性。



## 学习资料
1. [C/C++ 命令解析：getopt 方法详解和使用示例](https://blog.csdn.net/afei__/article/details/81261879)
1. [riscv交叉编译器版本问题](https://blog.csdn.net/u014558361/article/details/135372254)
1. [谨以此写下本人安装riscv的全过程 简单易懂！！](https://blog.csdn.net/qq_41976613/article/details/89629372)
2. [riscv-gnu-toolchain 交叉编译器如何构建？](https://www.zhihu.com/question/560687334/answer/3281645780?utm_id=0)
3. [RISC-V GNU编译环境搭建与运行实践](https://blog.csdn.net/ALLap97/article/details/112373544)
4. [安装 RISC-V 交叉编译工具链](https://soc.ustc.edu.cn/CECS/lab0/riscv/)
5. [对于RISV -V处理器，详解 gcc 编译器，Makefile中gcc编译器参数的含义_gcc riscv](https://zhuanlan.zhihu.com/p/660618423)
6. [Verilog 有符号数间，及有符号数与常数比较大小](https://blog.csdn.net/sinat_29862967/article/details/119829214)
7. [Verilog算术右移](https://blog.csdn.net/qq_41634276/article/details/80414488) 
8. [循序渐进，学习开发一个RISC-V上的操作系统 - 汪辰 - 2021春](https://www.bilibili.com/video/BV1Q5411w7z5?p=14&vd_source=d791a57f43dad7ca6a1d62950cab7001) 

---

## Register
<div align="center"> <img src="./pic/riscv1.png"  width="600"/> 
<div align="center"> <img src="./pic/riscv2.png"  width="1000"/> 
<div align="center"> <img src="./pic/riscv3.png"  width="1000"/> 