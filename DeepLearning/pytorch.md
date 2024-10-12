<!-- GFM-TOC -->
- [类之间的关系](#类之间的关系)
- [API](#api)
<!-- GFM-TOC -->
---

### 类之间的关系
    * 类LinearModel ---- 继承 ----> 类torch.nn.Module
    * 类LinearModel ---- 包含 ----> 类linear

### API
  * [torch.nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html#torch.nn.Linear)
  * [torch.nn.MSELoss](https://pytorch.org/docs/stable/generated/torch.nn.MSELoss.html)、[torch.nn.BSELoss]、[torch.nn.CrossEntropyLoss]
    * `torch.nn.CrossEntropyLoss`包含了`softmax`，需要输入的是最后一层线性层的输出，和标签`lables`。`lables`不是独热码，其特征是一维的，且数值表示的是独热码中1的位置。
  * [torch.optim.SGD](https://pytorch.org/docs/stable/generated/torch.optim.SGD.html)
  * [torch.nn.Sigmoid](https://pytorch.org/docs/stable/generated/torch.nn.Sigmoid.html)、[torch.nn.ReLU]
    * `torch.nn.Sigmoid()`是一种`class`。
    * `torch.sigmoid()`和`torch.nn.functional.sigmoid()`是作用一模一样的函数，可以直接使用。


  
[pytorch中.numpy()、.item()、.cpu()、.detach()及.data的使用](https://blog.csdn.net/gary101818/article/details/124658826)