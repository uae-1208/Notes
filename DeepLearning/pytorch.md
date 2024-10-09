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
  * [torch.nn.MSELoss](https://pytorch.org/docs/stable/generated/torch.nn.MSELoss.html)、[torch.nn.BSELoss]
  * [torch.optim.SGD](https://pytorch.org/docs/stable/generated/torch.optim.SGD.html)
  * [torch.nn.Sigmoid](https://pytorch.org/docs/stable/generated/torch.nn.Sigmoid.html)、[torch.nn.ReLU]
    * `torch.nn.Sigmoid()`是一种`class`。
    * `torch.sigmoid()`和`torch.nn.functional.sigmoid()`是作用一模一样的函数，可以直接使用。


  
[pytorch中.numpy()、.item()、.cpu()、.detach()及.data的使用](https://blog.csdn.net/gary101818/article/details/124658826)