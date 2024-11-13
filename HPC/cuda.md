<!-- GFM-TOC -->
- [核函数相关](#核函数相关)
- [内存管理](#内存管理)
- [内存分布](#内存分布)
- [异步、同步技术](#异步同步技术)
- [原子操作](#原子操作)
- [待学习](#待学习)
<!-- GFM-TOC -->
---


### 核函数相关
1. `__global__`修饰的函数是`kernel`，这些函数在GPU上执行 ，但是需要在CPU端调用。
2. CPU调用一个核函数的方式：
    > kernel<<<block_dim, thread_dim>>>(...)
3. `threadIdx`, `blockIdx`, `blockDim`, `gridDim`
   1. [cuda中threadIdx、blockIdx、blockDim和gridDim的使用）](https://zhuanlan.zhihu.com/p/544864997)
4. dim(w, 1, 1) == dim(w) == w


### 内存管理
* cudaMalloc
  * 分配的内存位于global memory。
  * 注意传入的第一个参数是**地址变量的指针**，也就是一个双重指针。
* cudaFree
* cudaMemcpy
* cudaMemset
* cudaDeviceSynchronize


### 内存分布
* __shared__
  * 假如某block当中有100个threads，且kernel在变量a的声明中添加了前缀`__shared__`，这并不意味这存在100个变量a，而是在该block的共享内存中存在一个变量a，a可被这100个线程共同访问。


### 异步、同步技术
* __syncthreads()
  * 确保线程块中的每个线程都执行完kernal __syncthreads()前面的语句后，才会执行下一条语句。
* cudaDeviceSynchronize()
  * 阻塞host程序，**所有异步流**中的device kernal执行完后才会继续执行host程序。
  * 避免CPU访问哪些还未被GPU处理好的数据。
* cudaStreamSynchronize(stream)
  * 阻塞host程序，当**指定流**中的device kernal执行完后才会继续执行host程序。
* cudaMemsetAsync()....


### 原子操作
* atomicADD/SUB/AND/OR/XOR
  * 只允许特定的操作
  * 只运行integer



### 待学习
1. [CUDA相关 | 如何安装deviceQuery](https://zhuanlan.zhihu.com/p/666647168)
2. [CUDA编程 - Nsight system & Nsight compute 的安装和使用 - 学习记录](https://blog.csdn.net/weixin_40653140/article/details/136238420)
3. cuBLAS