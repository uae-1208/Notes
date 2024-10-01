<!-- GFM-TOC -->
- [任务](#任务)
- [模型](#模型)
- [优化方法](#优化方法)
- [误差](#误差)
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

### 优化方法
  1. 梯度下降
     * 梯度下降不一定能得到最优解，有可能是局部最优解
  2. SGD 随机梯度下降
     * 利用随机样本的噪声，推动训练越过鞍点。
  3. mini batches
     * update：更新一次参数。
     * epoch：遍历一次所有的batches


### 误差
  1. MSE 均方误差
  2. BCE 二分类交叉熵 












　　
　　