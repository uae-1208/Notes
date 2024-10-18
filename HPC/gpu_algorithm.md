<!-- GFM-TOC -->
- [Reduce](#reduce)
- [Scan](#scan)
- [Scan的应用](#scan的应用)
- [Sort](#sort)
<!-- GFM-TOC -->
---


step and work


### Reduce
1.  必须满足：`binary` and `associate`
2.  step为O(logn)，work为O(n)。


### Scan
1. 隐式scan和显式scan
   1. 二者串行代码的差异就是循环体内的语句颠倒一下。
   2. **隐式scan和显式scan的转换**：显式scan的结果与原序列每个对应元素进行一次operation就是隐式scan的结果。
2. `operator`的特征值
3. 实现scan算法的并行性：
   1. 对每一个输出元素的计算都进行reduce加法操作：step为`O(logn)`，work为`O(n^2)`。 
   2. `Hillis and Steele`：step为`O(logn)`，work为`O(nlogn)`。
   3. `Blelloch`：：step为`O(logn)`，work为`O(nlogn)`。
      1. `downsweep`和上半部分的reduce是镜像的
      2. Blelloch的step为`O(2log2n)`，是Hillis and Steele的两倍，但是work更少。
   4. [CUDA学习（二）：并行扫描Scan](https://blog.csdn.net/qq_44161734/article/details/134620943)
   5. [CUDA练手小项目——Parallel Prefix Sum (Scan)](https://zhuanlan.zhihu.com/p/661460705)


### Scan的应用
1. istogram
2. compact
   1. 简单来说就是从输入数组中提取出符合条件的部分数据，并移入到输出数组当中。
   2. 步骤
      1. 并行地对每个元素进行条件判断。若data_in[i]符合条件，则c=1。
      2. 对数组temp进行显式scan的加操作。scan[i]的值是`符合条件的data_in[i]在data_out中的对应位置`,且data_in[i]==1时scan[i]才有效。
      3. 若想把不符合条件的data_in[i]放到符合条件的data_in[i]后面，则参考：[GPU排序（基数排序Radix Sort）小节【理论篇】](https://zhuanlan.zhihu.com/p/491798194)2.1部分。
3. 段扫描
4. 稀疏矩阵的乘法
   1. 最后计算每个元素的值时需要使用到段扫描。



### Sort
1. Bubble Sort
   * step complexity = O(n)
   * work complexity = O(n^2)
   * 简单
2. Merge Sort
   * 三个阶段采用三种不同处理方式
3. Bitonic Sort
   * 也就是排序网
4. Radix Sort
   * 对符合的元素进行`compact`操作。
   * 也可以一次性比较`n bits`，从而分成`2^n`路
5. [GPU排序（基数排序Radix Sort）小节【理论篇】](https://zhuanlan.zhihu.com/p/491798194)
6. [冒泡、归并、双调排序的GPU实现](https://blog.csdn.net/lixiaoguang20/article/details/77987826)