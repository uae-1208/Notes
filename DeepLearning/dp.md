<!-- GFM-TOC -->
- [任务](#任务)
- [模型](#模型)
- [优化list](#优化list)
- [误差](#误差)
- [优化地图](#优化地图)
- [Optimization Issue](#optimization-issue)
- [CNN](#cnn)
<!-- GFM-TOC -->
---


### 任务
  1. regression
  2. classification 
  3. structured learning -- 例如gpt

### 模型
* ‌机器学习就是找function
  * model就是带未知参数的function
  * models:
    * Linear Model
    * Logistic Regression 
      * 激活函数为`sigmod()`
    * Rectified Linear Unit  
      * 激活函数为`RelU()`
    * 分类问题
      * 激活函数为`softmax()`
    * [CNN](#cnn)卷积层和池化层
  

### 优化list
  1. 梯度下降
     * 梯度下降不一定能得到最优解，有可能是局部最优解
  2. SGD 随机梯度下降
     * 利用随机样本的噪声，推动训练越过鞍点。
  3. mini batches
  4. momentum 
  5. learning rate
  6. batch normalization


### 误差
  1. MSE 均方误差
  2. BCE 二分类交叉熵 
  3. CE 交叉熵

### 优化地图
  1. training loss too large
     1. model bias
        * 让model变得更复杂。
     2. [Optimization Issue](#optimization-issue)
        * [优化list](#优化list)
  2. training loss too small
     1. testing loss too large
        1. overfitting
             * 扩充训练集。
               1. 搜集更多的数据。
               2. data augmentation。 
             * 让model变得更简单（因为此时已经力大砖飞了）。
               1. 调整方向
                  1. less parameters。
                  2. less features。
                  3. early stopping。
                  4. regularzation。
                  5. dropout。
               2. 调整手段
                  1. N折叠交叉验证。
        2. mismatch（训练集和测试集数据的分步差异很大，例如训练集是真人，测试集是卡通动漫人物）


### Optimization Issue
1. critical point：鞍点和局部最小点
   1. 在高维空间中，鞍点的数量要比局部最小点多不少。  
      * 这里的理解可以参考三体中的君士坦丁堡故事。
   2. 可以利用海森矩阵H来区分鞍点和局部最小点：`H是正定矩阵or负定矩阵->局部最小点；反之->鞍点`。
   3. 也可以利用H矩阵帮助训练走出鞍点。
      1. 找到一个H的负特征值以及对应的特征向量u.
      2. 参数往u的方向更新。
      * 运算量太大，不推荐。
2. mini batch
     * update：更新一次参数。epoch：遍历一次所有的batches。
     * 每个batch的分布有细微差异，因此一个batch loss中的critical point在另一个batch loss中可能就不再是了。
     * mini batch甚至还能提高测试的准确率。（[类神经网络训练不起来怎么办(二)批次(batch)与动量(momentum)](https://www.bilibili.com/video/BV1J94y1f7u5/?vd_source=d791a57f43dad7ca6a1d62950cab7001&spm_id_from=333.788.videopod.episodes&p=14) 17min）
3. momentum 
   * 利用惯性冲出critical point的范围。
4. learning rate
   * 在训练时，实际上走到critical point是很苦难的，大部分时loss都是在critical point附近振荡。=》学习率在训练后期时太大了。
   1. RMS
   2. RMSProp
   3. Adam: RMSProp + Momentum
   4. Learning Rate Scheduling
5. batch normalization


### CNN
  1. 利用卷积算子可以捕捉邻近像素值的相关性（或者说，图形的局部特征），这是把所有像素拼接成一维feature向量无法做到的。
  2. 全连接层的权重参数个数一般远大于卷积层。
     1. 因为卷积层的参数可以被重复利用。
  3. padding
     1. 随着一次次卷积运算的进行，图像会越来越小，如果神经网络深度很大，图像可能变得特别特别小。
     2. 覆盖边缘和角落像素点的过滤器远远比中间像素点少，导致丢失图像边缘位置的信息。
  4. stride
  5. kernel_size
     1. 在CNN中，进行卷积操作时一般会以卷积核模块的一个位置为基准进行滑动，这个基准通常就是卷积核模块的中心。若卷积核为奇数，卷积锚点很好找，自然就是卷积模块中心，但如果卷积核是偶数，这时候就没有办法确定了，让谁是锚点似乎都不怎么好。
  6. 卷积的计算公式
     * o = ⌊(n + 2p - f) / s⌋ + 1
       * 图片尺寸：nxn
       * 卷积核的大小：fxf
       * 填充（Padding）：p
       * 步长(Stride)：s
       * 输出图片的尺寸：oxo











　　
　　